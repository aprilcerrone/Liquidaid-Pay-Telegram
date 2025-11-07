# Liquidaid Pay Telegram - n8n Workflows

Production-ready n8n workflows for the Liquidaid Pay Telegram bot automation system.

## 📋 Overview

This repository contains enterprise-grade n8n workflows for automating bill notifications and recycling rewards within the Liquidaid Pay Telegram bot ecosystem.

## 🚀 Workflows

### 1. Recycling Rewards
Automated recycling verification and reward system that processes user-submitted recycling photos, verifies submissions through Supabase, calculates rewards, and notifies users.

**Features:**
- Photo validation and processing
- Location-based verification
- Automatic reward calculation (R5/kg, min R10, max R500)
- Supabase JSONB verification integration
- Telegram bot integration

**Workflow ID:** `GlbsvKroPDnLqw9o`

### 2. Bill Notifications
Scheduled daily notifications for bills due soon and overdue bills, automatically sent to users via Telegram.

**Features:**
- Daily scheduled trigger (9 AM)
- Bills due within 7 days detection
- Overdue bills detection
- Personalized notification messages
- User-friendly formatting

**Workflow ID:** `DmI090uTUTcK4gFw`

## 📁 Repository Structure

```
Liquidaid-Pay-Telegram/
├── workflows/                    # n8n workflow JSON files
│   ├── recycling-rewards.json    # Recycling rewards workflow
│   └── bill-notifications.json   # Bill notifications workflow
├── docs/                         # Documentation
│   ├── recycling-rewards.md      # Recycling workflow documentation
│   └── bill-notifications.md     # Bill notifications documentation
├── .env.example                  # Environment variables template
├── .gitignore                    # Git ignore rules
└── README.md                     # This file
```

## 🛠️ Setup & Installation

### Prerequisites

- n8n instance (v1.0+)
- Supabase account and project
- Telegram Bot Token
- Access to Liquidaid Pay database

### Environment Variables

Copy `.env.example` to `.env` and configure:

```bash
# Supabase Configuration
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key

# Telegram Configuration
TELEGRAM_BOT_TOKEN=your-telegram-bot-token

# n8n Configuration (if self-hosted)
N8N_URL=https://your-n8n-instance.com
```

### Installation Steps

1. **Clone the repository:**
   ```bash
   git clone https://github.com/aprilcerrone/Liquidaid-Pay-Telegram.git
   cd Liquidaid-Pay-Telegram
   ```

2. **Import workflows to n8n:**
   - Open your n8n instance
   - Go to Workflows → Import from File
   - Import `workflows/recycling-rewards.json`
   - Import `workflows/bill-notifications.json`

3. **Configure credentials:**
   - Set up Supabase credentials in n8n
   - Set up Telegram Bot credentials in n8n
   - Configure environment variables in n8n

4. **Activate workflows:**
   - Activate each workflow after importing
   - Test workflows in test mode first
   - Monitor execution logs

## 🔧 Configuration

### Recycling Rewards Workflow

**Webhook Endpoint:** `/recycling-rewards`

**Required Supabase Functions:**
- `create_verification_jsonb` - Creates verification request
- `perform_verification_jsonb` - Performs verification checks

**Database Tables:**
- `telegram_users` - User information
- `recycling_submissions` - Submission records
- `recycling_rewards` - Reward transactions
- `recycling_verifications_jsonb` - Verification data

### Bill Notifications Workflow

**Schedule:** Daily at 9:00 AM

**Database Tables:**
- `bills` - Bill records
- `telegram_users` - User information

**Notification Types:**
- `overdue` - Bills past due date
- `due_today` - Bills due today
- `due_soon` - Bills due within 3 days
- `reminder` - Bills due within 7 days

## 📖 Documentation

Detailed documentation for each workflow:

- [Recycling Rewards Workflow](./docs/recycling-rewards.md)
- [Bill Notifications Workflow](./docs/bill-notifications.md)

## 🔒 Security

- All sensitive credentials are stored in n8n's credential system
- Environment variables should never be committed to version control
- Use `.env.example` as a template only
- Regularly rotate API keys and tokens

## 🧪 Testing

### Testing Recycling Rewards Workflow

1. Send a photo with location to Telegram bot
2. Verify webhook receives the request
3. Check Supabase for verification record
4. Verify reward calculation
5. Confirm Telegram notification sent

### Testing Bill Notifications Workflow

1. Create test bills in database
2. Manually trigger workflow or wait for scheduled time
3. Verify notifications sent to correct users
4. Check notification content and formatting

## 📊 Monitoring

Monitor workflow executions in n8n:
- Execution history
- Error logs
- Performance metrics
- Success rates

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is proprietary and confidential. All rights reserved.

## 👥 Team

- **Project:** Liquidaid Pay
- **Organization:** Incorporaid
- **Repository:** [Liquidaid-Pay-Telegram](https://github.com/aprilcerrone/Liquidaid-Pay-Telegram)

## 🆘 Support

For issues and questions:
- Create an issue in this repository
- Contact the development team
- Check n8n documentation: https://docs.n8n.io

## 📅 Changelog

### v1.0.0 (2025-11-07)
- Initial release
- Recycling Rewards workflow
- Bill Notifications workflow
- Production-ready documentation

---

**Status:** ✅ Production Ready
**Last Updated:** 2025-11-07
