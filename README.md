.
# Automated Coupon Campaign System (n8n)

An automated coupon distribution system built using **n8n**, **Google Sheets**, and **Gmail**.

This workflow automates the entire lifecycle of a coupon campaign including:

- Reading recipients from a Google Sheet
- Generating unique coupon codes
- Logging coupons into a tracking sheet
- Sending personalized coupon emails
- Sending reminder emails before expiry
- Automatically expiring coupons
- Sending a daily admin digest email summarizing campaign activity

This project demonstrates how **low-code automation tools like n8n can be used to build marketing automation systems**.

---

# Project Overview

The system consists of **two workflows**:

1. **Main Coupon Campaign Workflow**
2. **Admin Email Digest Workflow**

The main workflow handles coupon generation and user emails, while the digest workflow sends a consolidated summary to the admin.

---

# Architecture


Trigger
↓
Read Recipients (Google Sheets)
↓
Filter Eligible Users
↓
Configure Coupon Parameters
↓
Generate Coupon Code
↓
Prepare Coupon Data
↓
Log Coupon in Google Sheets
↓
Send Coupon Email
↓
Wait Until Reminder Time
↓
Send Reminder Email
↓
Wait Until Expiry
↓
Expire Coupon in Google Sheets
↓
Notify User of Expiry
↓
Execute Workflow → Admin Digest Workflow


Admin Digest Workflow:


Execute Workflow Trigger
↓
Read Coupons Sheet
↓
Build Daily Summary
↓
Send Admin Digest Email


---

# Features

### Automated Coupon Generation
Each recipient receives a unique coupon code generated dynamically.

### Email Automation
The workflow sends three types of emails:

1. Coupon Email
2. Expiry Reminder Email
3. Coupon Expiry Notification

### Google Sheets Integration
Google Sheets is used as the campaign database.

Two sheets are used:

- Recipients sheet
- Coupons tracking sheet

### Automated Coupon Lifecycle
Coupons automatically:

- activate
- send reminders
- expire

### Admin Digest
At the end of the workflow a **summary email** is sent to the admin containing:

- Total coupons issued
- Total expired coupons
- Active coupons
- Full table of today's coupons

---

# Tech Stack

| Tool | Purpose |
|-----|------|
| n8n | Workflow automation engine |
| Google Sheets | Data storage |
| Gmail | Email delivery |
| JavaScript | Coupon generation logic |

---

# Folder Structure


n8n-coupon-automation
│
├── workflows
│   ├── coupon-workflow.json
│   └── email-digest.json
│
├── README.md
├── CONTRIBUTING.md
├── LICENSE
└── .gitignore


---

# Workflow Components

## 1. Trigger

Two triggers are used:

- Manual Trigger (for testing)
- Schedule Trigger (daily campaign execution)

Cron Example:


0 9 * * *


Runs every day at **9 AM**.

---

## 2. Read Recipients

Reads recipients from Google Sheets.

Required columns:

| Column | Description |
|------|-------------|
| Name | Recipient name |
| Email | Email address |
| DiscountPercent | Discount value |
| Opted_In | Must be YES |
| Status | Blank for new users |

---

## 3. Filter Eligible Users

Filters users based on:

- Status is not ACTIVE
- Email is not empty
- Opted_In = YES

---

## 4. Coupon Configuration

Campaign settings are centralized in one node.

| Parameter | Description |
|----------|-------------|
| discountPercent | Discount amount |
| validityHours | Coupon validity |
| reminderHours | Reminder timing |
| storeUrl | Store link |
| notificationEmail | Admin email |

Example test values:


validityHours = 0.0167
reminderHours = 0.008


Production values:


validityHours = 24
reminderHours = 2


---

## 5. Coupon Code Generator

A JavaScript node generates unique coupon codes using patterns such as:

- SAVE + initials
- VIP + random string
- EMAILPREFIX + discount
- FLASH + random characters

Example output:


VIPZK7J60


---

## 6. Coupon Tracking Sheet

Coupons are stored in a separate sheet.

Columns:

| Column | Description |
|------|-------------|
| CouponCode | Generated coupon |
| UserEmail | Recipient email |
| UserName | Name |
| DiscountPercent | Discount applied |
| Status | ACTIVE / EXPIRED |
| CreatedAt | Creation timestamp |
| ExpiresAt | Expiry timestamp |
| CouponType | PERSONALIZED |
| UsageCount | Usage counter |
| MaxUsage | Maximum usage |

---

# Email Templates

The workflow uses **HTML email templates** for:

### Coupon Email

Includes:

- coupon code
- discount percentage
- expiry time
- store link

### Reminder Email

Sent shortly before expiry.

Creates urgency for the user.

### Expiry Notification

Sent after the coupon expires.

---

# Admin Digest

The admin digest contains:

- total coupons issued today
- expired coupons
- active coupons
- table of recipients

Example summary email:


Daily Coupon Digest
2026-03-09

5 issued
3 expired
2 active


---

# Setup Instructions

## 1 Install n8n

You can run n8n using:


npm install n8n -g
n8n start


Or use n8n cloud.

---

## 2 Import Workflows

In n8n:

1. Open the editor
2. Click menu
3. Import workflow
4. Upload JSON file

Import both:


coupon-campaign.json
email-digest.json


---

## 3 Setup Credentials

Create credentials for:

### Google Sheets OAuth2

Used for reading and writing spreadsheet data.

### Gmail OAuth2

Used for sending all campaign emails.

---

## 4 Setup Google Sheet

Create a Google Sheet with two tabs.

### Tab 1: Discounted

Input recipients.

Example row:


Akhil Singh | akhil@email.com
 | 60 | YES |


### Tab 2: Coupons

Tracking sheet for generated coupons.

---

# Testing

Recommended testing configuration:


validityHours = 0.0167
reminderHours = 0.008


Run the workflow manually.

Expected behavior:

1. Coupon email sent
2. Reminder email sent
3. Coupon expires
4. Expiry email sent
5. Admin digest sent

---

# Known Issues

Some users may experience:

- workflow trigger configuration errors
- incorrect sheet column names
- missing credentials
- incorrect filter conditions

Contributions that fix these issues are welcome.

---

## License

This project is licensed under the Creative Commons Attribution-NonCommercial 4.0 License.

The workflow can be forked and modified for learning or contribution purposes, but commercial use is not permitted.

