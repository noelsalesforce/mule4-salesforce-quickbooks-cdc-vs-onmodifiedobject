# Salesforce Change Data Capture (CDC) to QuickBooks Integration

## Overview
This MuleSoft application integrates Salesforce Change Data Capture (CDC) with QuickBooks Online (QBO). It listens for changes to the Title field on Salesforce Contact records and updates or creates corresponding customers in QuickBooks.

## Features

### Flow 1: CDC Listener for Contact Updates
- Listens for Title field changes in Salesforce.
- Queries Salesforce for additional contact details.
- Updates the existing QuickBooks customer if an ID is present.
- Logs cases where QuickBooks IDs are missing.

### Flow 2: On Modified Object
- Listens for modifications to Salesforce Contact records.
- Retrieves the QuickBooks Customer ID.
- If the customer exists, updates it in QuickBooks.
- If the customer does not exist, creates a new customer in QuickBooks.
- Logs events for tracking updates and new creations.

## Flow Details

### Flow 1: CDC Listener for Title Updates
- **Replay Channel Listener** – Captures CDC events for Title updates.
- **Transform Message** – Prepares the payload for processing.
- **Choice Router** – Determines if the QuickBooks Customer ID exists:
    - If the ID exists → Update QuickBooks customer.
    - If the ID is missing → Log the event.

### Flow 2: On Modified Object
- **On Modified Object Listener** – Detects changes in Salesforce contacts.
- **Set Variables** – Stores the contact record and QuickBooks Customer ID.
- **Get Customer by ID** – Retrieves customer details from QuickBooks.
- **Choice Router** – Determines if the customer exists:
    - If no QuickBooks ID is found → Create a new customer in QuickBooks.
    - If QuickBooks ID exists → Update customer in QuickBooks.
- Logs each event.

## Salesforce CDC vs Polling

### Salesforce Integration with MuleSoft
This project contains MuleSoft flows to integrate Salesforce with QuickBooks Online (QBO). It includes two key operations using the Salesforce Connector:
- **Change Data Capture (CDC)**
- **On Modified Object**

### Flows Included
1. **Flow - OnModifiedObject**:
    - Triggers when a Salesforce object is modified.
    - Retrieves the contact record and QuickBooks customer ID.
    - Determines whether to create or update the customer in QBO based on the retrieved data.
  
2. **Flow - Change Data Capture (CDC)**:
    - Listens for real-time changes in Salesforce objects.
    - Captures all field updates, including deletes and undeletes.
    - Sends the updates to QuickBooks Online.

### Comparison: Change Data Capture (CDC) vs. On Modified Object

| Feature                              | Salesforce Change Data Capture (CDC) | On Modified Object          |
|--------------------------------------|--------------------------------------|-----------------------------|
| **Trigger Mechanism**                | Event-driven                         | Polling-based               |
| **How it works**                     | Listens for real-time changes in specified objects | Periodically checks for modifications on objects |
| **Latency**                          | Low (near real-time)                 | Higher (depends on polling frequency) |
| **Use Case**                         | Best for real-time integrations where immediate action is required (e.g., sync changes instantly) | Best for batch-oriented processing or when polling is preferred over event-based triggering |
| **Data Captured**                    | Tracks all field changes, including deletes and undeletes | Detects only modified objects, based on specific fields |
| **Performance Impact**               | Efficient since it relies on Salesforce’s event system | Can be resource-intensive if polling frequently |
| **Setup Complexity**                 | Requires Change Data Capture (CDC) to be enabled in Salesforce | Easier to set up, as it does not require additional Salesforce configuration |
| **History & Replayability**          | Supports replay of events (up to 3 days) using replay IDs | No replay mechanism; only detects changes since the last poll |
| **API Limits**                       | Uses Salesforce’s Event Bus, reducing API usage | Relies on API calls for each polling interval |


### Notes
- **CDC** is recommended for real-time use cases to avoid excessive API calls.
- The **On Modified Object** approach may be suitable if you do not have CDC enabled or need scheduled polling.

