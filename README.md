
# Maintenance Reminder with Power Automate & Teams

This project provides a **maintenance reminder solution** based on  
- **SharePoint List** (as central data source)  
- **Power Automate Flows** (logic + notifications)  
- **Microsoft Teams Adaptive Cards** (user interaction)  

---

## SharePoint List Schema
List name: **Test Maintenance Reminder**

| Column           | Type          | Description |
|------------------|---------------|-------------|
| Instrument       | Text          | Instrument name |
| MaintenanceType  | Choice        | Internal / External |
| Provider         | Text          | Vendor or internal team |
| S/N              | Text          | Serial number |
| LastMaintenance  | Date          | Last performed maintenance |
| IntervalMonths   | Number        | Interval in months |
| NextDueDate      | Date          | Calculated due date |
| ReminderThreshold| Number        | Days before due date to remind |
| AssignedTo       | Person/Group  | Assigned team member |
| Status           | Choice        | Open / In Progress / Done |
| DaysLeft         | Calculated    | `NextDueDate - Today()` |

---

##  Power Automate Flows

### Flow 1 – Maintenance Reminder
- **Trigger:** Scheduled (daily)  
- **Logic:**
  1. Get items from SharePoint  
  2. Filter: `Status = Open` and `DaysLeft <= ReminderThreshold`  
  3. Send Adaptive Card in Teams (`AssignedTo`)  
  4. Wait for response  
  5. Update SharePoint:  
     - If **Assign to me** → `AssignedTo = responder`, `Status = In Progress`  
     - If **Mark as done** → `Status = Done`, `LastMaintenance = Today()`, recalc `NextDueDate`  

### Flow 2 – Follow-up after Assignment
- **Trigger:** Response from Flow 1 (*Assign to me*)  
- **Logic:**
  1. Send follow-up Adaptive Card (only **Mark as done**)  
  2. On completion → update SharePoint `Status = Done`, update maintenance dates  

---

## 📂 Repository Structure

---

## 📂 Adaptive Cards
- [`adaptive-cards/reminder-card.json`](adaptive-cards/reminder-card.json) – Card with "Assign to me" + "Mark as done"  
- [`adaptive-cards/followup-card.json`](adaptive-cards/followup-card.json) – Card with only "Mark as done"  

---

## Setup Guide
1. Create SharePoint List based on schema above  
2. Import Power Automate flows from `/flows/`  
3. Adjust SharePoint connection & Teams channel in flows  
4. Upload Adaptive Card JSON from `/adaptive-cards/` into the flow actions  
5. Test with sample data  

---

## 🛠️ Troubleshooting
- **No reminder triggered?** → Check calculated `DaysLeft` column in SharePoint  
- **Card not showing in Teams?** → Ensure Adaptive Card JSON is valid (use [Adaptive Card Designer](https://adaptivecards.io/designer/))  
- **Date not updating?** → Verify that `LastMaintenance` and `NextDueDate` are recalculated correctly in Flow  

---

## 📘 Documentation
- [Architecture](docs/architecture.md)  
- [Setup Guide](docs/setup-guide.md)  
- [Adaptive Card Samples](docs/adaptive-card-samples.md)  
- [Troubleshooting](docs/troubleshooting.md)  

---

With this solution, maintenance reminders are automated, tracked in SharePoint, and integrated into Teams for easy task management.
