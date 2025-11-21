# Smart Messaging Dashboard - Deployment Guide

## Project Overview
A React dashboard displaying smart messaging metrics from a GraphQL API with beautiful visualizations.

## What's Built
- ✅ React 18 + TypeScript + Vite
- ✅ Tailwind CSS for styling
- ✅ Recharts for data visualization
- ✅ GraphQL API integration
- ✅ Loading states and error handling
- ✅ VPN-aware (only works on company VPN)

## Deployment to Vercel

### Method 1: Via Vercel Dashboard (Recommended)

1. Go to https://vercel.com/new
2. Click "Import Git Repository"
3. Select `malcolmgoldsmith/DataOS`
4. **Important:** Set the project name to lowercase (e.g., `dataos2` or `smart-messaging`)
5. Vercel auto-detects Vite settings
6. Click **Deploy**

### Method 2: Via CLI

```bash
npx vercel login
npx vercel --prod
```

## Configuration Files

### vercel.json
Configured to proxy `/api/graphql` to the DataOS API endpoint. This handles CORS issues.

### vite.config.ts
Local development proxy configuration for the same API.

## Important Notes

### VPN Requirement
⚠️ The dashboard **requires VPN access** to work because the GraphQL API (`dataos.naea1.uds-dev.lenovo.com`) is only accessible on the corporate network.

**What this means:**
- ✅ Works locally when on VPN
- ✅ Works on Vercel when user is on VPN
- ❌ Shows error when not on VPN (this is intentional)

### GraphQL Warnings
The API returns some NaN/Infinity warnings for the `engagement_rate` field. These are handled gracefully:
- Warnings logged to console
- Data still displays correctly
- Invalid values filtered out

## Local Development

```bash
# Install dependencies
npm install

# Run dev server (requires VPN)
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

## Troubleshooting

### "Error loading data: HTTP error! status: 500"
- You're not on the VPN
- Connect to corporate VPN and refresh

### Vercel deployment errors
- Make sure project name is lowercase
- Check that latest code is pushed to GitHub
- Verify vercel.json is valid

### Charts not showing
- Check browser console for errors
- Verify API returns data (check Network tab)
- Ensure you're on VPN

## Architecture

```
src/
├── components/
│   └── dashboard/
│       ├── MessageMetricsCards.tsx  # Top 3 metric cards
│       ├── MessageCharts.tsx        # Pie charts
│       └── Header.tsx               # Page header
├── hooks/
│   └── useMessageData.ts           # GraphQL data fetching
└── App.tsx                         # Main app component
```

## API Integration

**Endpoint:** `https://dataos.naea1.uds-dev.lenovo.com/lens2/api/public:udc-sm/v2/graphql`

**Query:**
```graphql
query LensQuery {
  table(offset: 0, timezone: "UTC", orderBy: {}) {
    udc_smart_messages {
      msg_category
      msg_display
      number_of_messages_sent
      displayed
      clicked
      engagement_rate
    }
  }
}
```

**Metrics Calculated:**
- Total Messages Sent: Sum of `number_of_messages_sent`
- Messages Displayed: Sum of `displayed`
- Engagement Rate: `(clicked / displayed) * 100`

## Next Steps

1. Deploy to Vercel using Method 1 above
2. Share the Vercel URL with your team
3. Make sure they're on VPN before accessing
4. Monitor and iterate based on feedback
