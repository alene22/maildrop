# maildrop

Simple email sending with pre-configured SMTP - no setup required! 🚀

A lightweight wrapper around Nodemailer that automatically detects SMTP settings based on your email provider. Perfect for when you want Resend-like simplicity but need to use free SMTP services.

## Features

- ✨ **Zero Configuration** - Auto-detects SMTP settings for Gmail, Outlook, Yahoo, and more
- 🎯 **Resend-like API** - Familiar interface if you've used Resend
- 📦 **Lightweight** - Just a thin wrapper around Nodemailer
- 🔒 **TypeScript** - Full TypeScript support
- 🆓 **Free** - Uses your existing email account

## Installation

```bash
npm install maildrop
```

## Quick Start

### Method 1: Using Environment Variables (SIMPLEST! 🎯)

```typescript
import { drop } from 'maildrop';

// Set environment variables:
// MAILDROP_EMAIL=your-email@gmail.com
// MAILDROP_PASSWORD=your-app-password

const { data, error } = await drop({
  to: 'recipient@example.com',
  subject: 'Hello World',
  html: '<strong>It works!</strong>',
});

if (error) {
  return console.error({ error });
}

console.log({ data });
```

### Method 2: Quick Drop Function (No Class!)

```typescript
import { quickDrop } from 'maildrop';

const { data, error } = await quickDrop(
  'your-email@gmail.com',
  'your-app-password',
  {
    to: 'recipient@example.com',
    subject: 'Hello World',
    html: '<strong>It works!</strong>',
  }
);
```

### Method 3: Class-Based (Original API)

```typescript
import { SimpleMail } from 'maildrop';

const mail = new SimpleMail('your-email@gmail.com', 'your-app-password');

const { data, error } = await mail.send({
  from: 'Your Name <your-email@gmail.com>',
  to: ['recipient@example.com'],
  subject: 'Hello World',
  html: '<strong>It works!</strong>',
});
```

## Supported Providers

The following email providers are automatically detected:

- ✅ **Gmail** (`gmail.com`, `googlemail.com`)
- ✅ **Outlook** (`outlook.com`, `hotmail.com`, `live.com`, `msn.com`)
- ✅ **Yahoo** (`yahoo.com`, `yahoo.co.uk`, `ymail.com`)
- ✅ **Zoho** (`zoho.com`, `*.zoho.com`)
- ✅ **Custom SMTP** (see below)

## Usage Examples

### Simplest: Environment Variables

Create a `.env` file:
```env
MAILDROP_EMAIL=your-email@gmail.com
MAILDROP_PASSWORD=your-app-password
```

Then use:
```typescript
import { drop } from 'maildrop';

// 'from' is optional - defaults to MAILDROP_EMAIL
const { data, error } = await drop({
  to: 'recipient@example.com',
  subject: 'Hello',
  html: '<p>Hello!</p>',
});
```

### Gmail

**Important:** Gmail requires an [App Password](https://support.google.com/accounts/answer/185833) instead of your regular password.

1. Enable 2-Step Verification on your Google Account
2. Generate an App Password: [Google Account Settings](https://myaccount.google.com/apppasswords)
3. Use the generated app password (16 characters, no spaces)

```typescript
import { SimpleMail } from 'maildrop';

const mail = new SimpleMail('your-email@gmail.com', 'your-16-char-app-password');

const { data, error } = await mail.send({
  from: 'Your Name <your-email@gmail.com>',
  to: 'recipient@example.com',
  subject: 'Hello from Gmail!',
  html: '<p>This email was sent using Gmail SMTP.</p>',
});
```

### Outlook / Hotmail

```typescript
const mail = new SimpleMail('your-email@outlook.com', 'your-password');

const { data, error } = await mail.send({
  from: 'Your Name <your-email@outlook.com>',
  to: ['recipient@example.com'],
  subject: 'Hello from Outlook!',
  html: '<p>This email was sent using Outlook SMTP.</p>',
});
```

### Custom SMTP

For providers not automatically detected, you can provide custom SMTP settings:

```typescript
const mail = new SimpleMail(
  'user@example.com',
  'password',
  {
    host: 'smtp.example.com',
    port: 587,
    secure: false, // true for 465, false for other ports
  }
);
```

### Multiple Recipients

```typescript
const { data, error } = await mail.send({
  from: 'sender@example.com',
  to: ['recipient1@example.com', 'recipient2@example.com'],
  cc: ['cc@example.com'],
  bcc: ['bcc@example.com'],
  subject: 'Hello',
  html: '<p>Hello everyone!</p>',
});
```

### Text and HTML

```typescript
const { data, error } = await mail.send({
  from: 'sender@example.com',
  to: 'recipient@example.com',
  subject: 'Hello',
  text: 'Plain text version',
  html: '<p>HTML version</p>',
});
```

### Verify Connection

Test your SMTP connection before sending:

```typescript
const mail = new SimpleMail('your-email@gmail.com', 'your-password');

const isValid = await mail.verify();
if (isValid) {
  console.log('SMTP connection is valid!');
} else {
  console.error('SMTP connection failed. Check your credentials.');
}
```

## API Reference

### `drop()` - Simplest Method! ⭐

Drop an email using environment variables. No credentials needed in code!

```typescript
drop(options: Omit<SendEmailOptions, 'from'> & { from?: string }): Promise<SendEmailResponse>
```

**Environment Variables Required:**
- `MAILDROP_EMAIL` - Your email address
- `MAILDROP_PASSWORD` - Your email password/app-password

**Options:**
- `to` (string | string[]) - Recipient email address(es) - **required**
- `subject` (string) - Email subject - **required**
- `html` (string, optional) - HTML content
- `text` (string, optional) - Plain text content
- `from` (string, optional) - Sender email address (defaults to `MAILDROP_EMAIL`)
- `cc` (string | string[], optional) - CC recipients
- `bcc` (string | string[], optional) - BCC recipients
- `replyTo` (string, optional) - Reply-to address

**Example:**
```typescript
import { drop } from 'maildrop';

const { data, error } = await drop({
  to: 'recipient@example.com',
  subject: 'Hello',
  html: '<p>Hello!</p>',
});
```

---

### `quickDrop()` - One-Liner Function

Drop an email without class instantiation.

```typescript
quickDrop(email: string, password: string, options: Omit<SendEmailOptions, 'from'> & { from?: string }): Promise<SendEmailResponse>
```

**Parameters:**
- `email` - Your email address
- `password` - Your email password/app-password
- `options` - Email options (same as `drop`, `from` defaults to `email`)

**Example:**
```typescript
import { quickDrop } from 'maildrop';

const { data, error } = await quickDrop(
  'user@gmail.com',
  'app-password',
  {
    to: 'recipient@example.com',
    subject: 'Hello',
    html: '<p>Hello!</p>',
  }
);
```

---

### `SimpleMail` - Class-Based API

#### Constructor

```typescript
new SimpleMail(email: string, password: string, customSMTP?: SMTPConfig)
```

- `email` - Your email address
- `password` - Your email password or app-specific password
- `customSMTP` - Optional custom SMTP configuration

#### Methods

##### `send(options: SendEmailOptions): Promise<SendEmailResponse>`

Send an email.

**Options:**
- `from` (string) - Sender email address
- `to` (string | string[]) - Recipient email address(es)
- `subject` (string) - Email subject
- `html` (string, optional) - HTML content
- `text` (string, optional) - Plain text content
- `cc` (string | string[], optional) - CC recipients
- `bcc` (string | string[], optional) - BCC recipients
- `replyTo` (string, optional) - Reply-to address

**Returns:**
```typescript
{
  data?: {
    id: string;
    messageId: string;
  };
  error?: {
    message: string;
    code?: string;
  };
}
```

##### `verify(): Promise<boolean>`

Verify SMTP connection. Returns `true` if connection is valid.

## Error Handling

Always check for errors:

```typescript
const { data, error } = await mail.send({
  from: 'sender@example.com',
  to: 'recipient@example.com',
  subject: 'Hello',
  html: '<p>Hello</p>',
});

if (error) {
  console.error('Failed to send email:', error.message);
  // Handle error
  return;
}

console.log('Email sent successfully:', data.messageId);
```

## Common Issues

### Gmail: "Username and Password not accepted"

- Make sure you're using an **App Password**, not your regular Gmail password
- Enable 2-Step Verification first
- Generate a new App Password if needed

### Outlook: Authentication failed

- Make sure you're using your full email address as the username
- Some accounts may require enabling "Less secure app access" (not recommended)
- Consider using an App Password instead

### Connection timeout

- Check your firewall settings
- Verify the SMTP port (587 for TLS, 465 for SSL)
- Some networks block SMTP ports

## License

ISC

## Contributing

Contributions welcome! Feel free to open issues or submit pull requests.

