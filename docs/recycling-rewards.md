# Recycling Rewards Workflow Documentation

## Overview

The Recycling Rewards workflow automates the verification and reward processing for user-submitted recycling photos through the Telegram bot.

## Workflow Flow

```
Webhook → Get User → Check Photo → Get File → Create Verification Request 
→ Extract Verification ID → Perform Verification Checks → Calculate Rewards 
→ Save Submission → Add Rewards → Success Message → Send Message → Webhook Response
```

## Nodes

### 1. Webhook
- **Type:** Webhook Trigger
- **Method:** POST
- **Path:** `/recycling-rewards`
- **Purpose:** Receives Telegram webhook requests with photo submissions

### 2. Get User
- **Type:** Supabase (Get)
- **Table:** `telegram_users`
- **Purpose:** Retrieves user information based on chat ID

### 3. Check Photo
- **Type:** Code
- **Purpose:** Validates photo presence and extracts photo/file ID and location data

### 4. Get a file
- **Type:** Telegram
- **Resource:** File
- **Purpose:** Downloads the photo file from Telegram

### 5. Create Verification Request
- **Type:** HTTP Request
- **Method:** POST
- **Endpoint:** `{{ $env.SUPABASE_URL }}/rest/v1/rpc/create_verification_jsonb`
- **Purpose:** Creates a verification request in Supabase

### 6. Extract Verification ID
- **Type:** Code
- **Purpose:** Extracts verification ID from the response

### 7. Perform Verification Checks
- **Type:** HTTP Request
- **Method:** POST
- **Endpoint:** `{{ $env.SUPABASE_URL }}/rest/v1/rpc/perform_verification_jsonb`
- **Purpose:** Executes verification rules and checks

### 8. Calculate Rewards
- **Type:** Code
- **Purpose:** Calculates rewards based on verified weight
  - Rate: R5 per kg
  - Minimum: R10
  - Maximum: R500
  - Points: 1 point per R1

### 9. Save Submission
- **Type:** Supabase (Insert)
- **Purpose:** Saves the recycling submission record

### 10. Add Rewards
- **Type:** Supabase (Insert)
- **Purpose:** Adds reward credits to user account

### 11. Success Message
- **Type:** Code
- **Purpose:** Formats success message with reward details

### 12. Send Message
- **Type:** Telegram
- **Purpose:** Sends success notification to user

### 13. Webhook Response
- **Type:** Respond to Webhook
- **Purpose:** Returns success response

## Verification Rules

1. **Minimum Weight Check:** Weight must be > 0 kg
2. **Duplicate Photo Check:** No duplicate photos within 24 hours
3. **Location Check:** Location data validation
4. **Photo Quality Check:** Photo quality validation
5. **Complete Submission Bonus:** Bonus for complete submissions

## Reward Calculation

```javascript
creditRate = 5; // R5 per kg
creditAmount = weightKg * creditRate;
points = Math.floor(creditAmount);
finalCredit = Math.max(10, Math.min(500, creditAmount));
```

## Database Schema

### Required Tables

- `telegram_users` - User information
- `recycling_submissions` - Submission records
- `recycling_rewards` - Reward transactions
- `recycling_verifications_jsonb` - Verification data (JSONB)

### Required Functions

- `create_verification_jsonb()` - Creates verification request
- `perform_verification_jsonb()` - Performs verification checks

## Error Handling

- Photo validation failures stop workflow
- User not found returns empty array
- Verification failures are logged
- All errors are captured in execution logs

## Testing

1. Send photo with location to Telegram bot
2. Verify webhook receives request
3. Check verification record in Supabase
4. Verify reward calculation
5. Confirm notification sent

## Troubleshooting

### Common Issues

1. **Photo not detected:** Check webhook payload structure
2. **User not found:** Verify chat_id in telegram_users table
3. **Verification fails:** Check Supabase function logs
4. **Reward not added:** Verify database permissions

## Environment Variables

- `SUPABASE_URL` - Supabase project URL
- `SUPABASE_ANON_KEY` - Supabase anonymous key

## Security

- All API calls use Bearer token authentication
- Sensitive data is not logged
- User data is validated before processing

## Performance

- Average execution time: 2-5 seconds
- Photo download: 1-3 seconds
- Verification: 0.5-1 second
- Database operations: 0.5-1 second

## Monitoring

Monitor:
- Execution success rate
- Average execution time
- Error frequency
- Verification success rate
- Reward calculation accuracy
