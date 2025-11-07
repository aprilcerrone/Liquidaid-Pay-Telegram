# Bill Notifications Workflow Documentation

## Overview

The Bill Notifications workflow sends automated daily notifications to users about bills that are due soon or overdue.

## Workflow Flow

```
Daily 9 AM Trigger → [Get Bills Due Soon, Get Overdue Bills] → Merge Bills 
→ Process Bills → Get User → Format Notification → Send Notification
```

## Nodes

### 1. Daily 9 AM Trigger
- **Type:** Schedule Trigger
- **Schedule:** `0 9 * * *` (Daily at 9:00 AM)
- **Purpose:** Triggers workflow daily at 9 AM

### 2. Get Bills Due Soon
- **Type:** Supabase (Get)
- **Table:** `bills`
- **Filters:** 
  - Due date >= today
  - Due date <= today + 7 days
  - Paid = false
- **Purpose:** Fetches bills due within 7 days

### 3. Get Overdue Bills
- **Type:** Supabase (Get)
- **Table:** `bills`
- **Filters:**
  - Due date < today
  - Paid = false
- **Purpose:** Fetches overdue bills

### 4. Merge Bills
- **Type:** Merge
- **Purpose:** Combines bills from both queries

### 5. Process Bills
- **Type:** Code
- **Purpose:** 
  - Calculates days until due
  - Determines notification type
  - Adds notification metadata

### 6. Get User
- **Type:** Supabase (Get)
- **Table:** `telegram_users`
- **Purpose:** Retrieves user information for notification

### 7. Format Notification
- **Type:** Code
- **Purpose:** Formats notification message based on notification type

### 8. Send Notification
- **Type:** Telegram
- **Purpose:** Sends formatted notification to user

## Notification Types

### Overdue
- **Condition:** Days until due < 0
- **Emoji:** ⚠️
- **Urgency:** "OVERDUE - X days past due"

### Due Today
- **Condition:** Days until due = 0
- **Emoji:** 🚨
- **Urgency:** "DUE TODAY"

### Due Soon
- **Condition:** 1 <= Days until due <= 3
- **Emoji:** ⏰
- **Urgency:** "Due in X days"

### Reminder
- **Condition:** 4 <= Days until due <= 7
- **Emoji:** 📅
- **Urgency:** "Due in X days"

## Notification Format

```
{Emoji} *Bill Notification*

{Urgency Message}

📋 {Bill Description}
💰 Amount: R {Amount}
📅 Due Date: {Formatted Date}

Please pay your bill to avoid any late fees.
```

## Database Schema

### Required Tables

- `bills` - Bill records
  - `id` (UUID)
  - `user_id` (UUID)
  - `description` (TEXT)
  - `amount` (NUMERIC)
  - `due_date` (TIMESTAMP)
  - `paid` (BOOLEAN)

- `telegram_users` - User information
  - `id` (UUID)
  - `chat_id` (BIGINT)
  - `username` (TEXT)
  - Other user fields

## Schedule Configuration

The workflow runs daily at 9:00 AM using cron expression:
```
0 9 * * *
```

To change the schedule, update the Schedule Trigger node with a new cron expression.

## Error Handling

- Bills without users are skipped
- Invalid dates are handled gracefully
- Missing user chat_id prevents notification
- All errors are logged in execution history

## Testing

### Manual Testing

1. Create test bills in database:
   - Bill due today
   - Bill due in 3 days
   - Overdue bill
   - Bill due in 10 days (should not trigger)

2. Manually trigger workflow in n8n

3. Verify notifications:
   - Check Telegram messages
   - Verify correct users received notifications
   - Check notification content

### Automated Testing

- Test with various due date scenarios
- Test with missing user data
- Test with paid bills (should not notify)
- Test notification formatting

## Performance

- Average execution time: 1-3 seconds per bill
- Batch processing: Processes all bills in parallel
- Scalability: Handles hundreds of bills efficiently

## Monitoring

Monitor:
- Daily execution success
- Number of bills processed
- Number of notifications sent
- Error rate
- User engagement (if tracking enabled)

## Customization

### Change Schedule

Update the Schedule Trigger node cron expression:
- Every 6 hours: `0 */6 * * *`
- Twice daily (9 AM, 6 PM): `0 9,18 * * *`
- Weekly (Monday 9 AM): `0 9 * * 1`

### Customize Notification Message

Edit the "Format Notification" Code node to modify:
- Message format
- Emojis used
- Urgency messages
- Additional information

### Add Notification Types

In the "Process Bills" Code node, add new notification types:
- Early reminder (14 days before)
- Last chance (1 day before)
- Custom urgency levels

## Troubleshooting

### Common Issues

1. **No notifications sent:**
   - Check if bills exist in database
   - Verify bills are not paid
   - Check user chat_id exists

2. **Wrong notification type:**
   - Verify due date calculation
   - Check notification type logic

3. **Missing users:**
   - Verify user_id in bills matches telegram_users.id
   - Check user has chat_id

4. **Schedule not triggering:**
   - Verify workflow is active
   - Check n8n schedule configuration
   - Verify timezone settings

## Environment Variables

No specific environment variables required (uses n8n credentials).

## Security

- User data is only accessed for notification purposes
- Bills data is filtered by user_id
- No sensitive financial data is exposed in logs

## Future Enhancements

- [ ] Email notifications option
- [ ] SMS notifications option
- [ ] Multiple notification channels
- [ ] Notification preferences per user
- [ ] Reminder escalation (multiple reminders)
- [ ] Payment link in notification
- [ ] Bill history in notification
