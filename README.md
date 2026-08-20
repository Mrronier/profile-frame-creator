# Profile Frame Creator

A production-ready Next.js web app for creating campaign profile pictures. An administrator uploads one transparent square PNG frame. Visitors upload their own photo, reposition, zoom, rotate and download the combined result as a 1080 × 1080 PNG.

Visitor photos are processed entirely in the browser. They are never sent to Vercel, Blob storage, a database or an external service.

## Technology

- Next.js App Router
- TypeScript
- Tailwind CSS
- React Easy Crop for touch and mouse positioning
- HTML Canvas for the final PNG
- Vercel Blob for the active frame
- Signed HTTP-only cookie for the single-admin session

## Run locally

Requirements: Node.js 22 or newer.

1. Install dependencies with `npm install`.
2. Copy `.env.example` to `.env.local`.
3. Add your local environment values.
4. Start the app with `npm run dev`.
5. Open the local address shown in the terminal.

The production check is `npm run build`.

## Environment variables

| Variable | Required | Purpose |
| --- | --- | --- |
| `NEXT_PUBLIC_SITE_URL` | Yes | Your final `https://` domain for canonical URLs and social metadata. |
| `ADMIN_PASSWORD` | Yes | Strong password used only for `/admin`. |
| `ADMIN_SESSION_SECRET` | Yes | Long random value used to sign the secure admin session cookie. |
| `BLOB_READ_WRITE_TOKEN` | Yes for admin uploads | Supplied by Vercel when Blob is connected. |
| `NEXT_PUBLIC_DEFAULT_FRAME_URL` | Optional | Public fallback frame URL if Blob has not been connected. |

Generate a session secret with a secure password manager or `openssl rand -base64 48`. Never commit real environment values.

## Deploy to Vercel

### GitHub method

1. Push this project to a private or public GitHub repository.
2. In Vercel, select **Add New → Project** and import the repository.
3. Keep the detected framework as **Next.js** and the default build settings.
4. Add the environment variables listed above for Production, Preview and Development as needed.
5. Deploy the project.

### Vercel dashboard upload

Create a new Vercel project from this source repository or use the Vercel CLI. No custom Node.js server, Docker configuration or custom build output is required.

## Connect Vercel Blob

1. Open the project in Vercel.
2. Go to **Storage** and create or connect a Blob store.
3. Connect the store to this project.
4. Confirm that `BLOB_READ_WRITE_TOKEN` is available in the project environment variables.
5. Redeploy after adding or changing environment variables.

The active frame is stored as `profile-frame/active.png`. Uploading a replacement updates the same managed asset.

## Connect your custom domain

1. Open the Vercel project and go to **Settings → Domains**.
2. Add your domain or subdomain.
3. Apply the DNS records shown by Vercel at your DNS provider.
4. Set `NEXT_PUBLIC_SITE_URL` to the exact final address, for example `https://frame.yourdomain.com`.
5. Redeploy so canonical and social metadata use the custom domain.

## Admin frame management

1. Visit `/admin` on the deployed domain.
2. Sign in with the value stored in `ADMIN_PASSWORD`.
3. Upload a transparent PNG with a square 1:1 ratio.
4. Review the preview and select **Upload and activate**.
5. Open the visitor creator to confirm the active frame.

The admin interface checks that the frame is square and includes transparency. Recommended size is 1080 × 1080 px or larger, with the portrait area transparent and campaign artwork around the outer edge.

The admin session expires after 12 hours. The password and signing secret remain server-side. The admin page and API routes are blocked from indexing.

## Visitor workflow

1. The app loads the current admin-managed frame.
2. The visitor selects a JPG, PNG, WebP or HEIC image up to 20 MB.
3. The photo remains in browser memory and is not uploaded.
4. Dragging, mouse-wheel zooming, pinch zooming and rotation happen locally.
5. The browser combines the adjusted photo and fixed frame into a 1080 × 1080 PNG.
6. The temporary photo is cleared when the page closes or refreshes.

## Customisation

- Brand colours and spacing: edit the variables at the top of `app/globals.css`.
- Page headings and button copy: edit `app/page.tsx` and `components/profile-frame-editor.tsx`.
- Export resolution: change `EXPORT_SIZE` in `components/profile-frame-editor.tsx`.
- Maximum visitor file size: change `MAX_FILE_SIZE` in the same component.
- Logo mark and brand name: edit the header in `app/page.tsx`.
- SEO title and description: edit `app/layout.tsx`.
- Favicon: replace `public/favicon.svg`.
- Social preview: replace `public/og.png` with another 1200 × 630 image.

## Browser support

The editor targets current Chrome, Safari, Firefox and Edge on desktop, iPhone, iPad and Android. HEIC conversion is loaded only when a HEIC or HEIF file is selected. Direct downloading depends on browser download support; modern mobile browsers may display their save or share interface.
