
# 📧 Mail Server Setup Guide (Self-Hosted Bulk Email Server)

This guide explains how to set up your own company mail server for bulk emailing using your machine with **16GB RAM** and a **static IP**.

---

## 🛠️ Step 1: Prepare the Server
- **OS**: Install a stable Linux distro → Ubuntu Server LTS or Debian.
- **Update**: 
  ```bash
  apt update && apt upgrade -y
  ```
- **Hostname**: Set FQDN to match your mail domain:
  ```bash
  hostnamectl set-hostname mail.unionsystechnologies.com
  ```

---

## 🛠️ Step 2: Network & DNS Setup
- **Static IP Mapping**: Ensure your firewall NAT maps **public static IP → server’s private IP**.
- **Reverse DNS (PTR Record)**: Ask your ISP to set rDNS for your static IP → should resolve to `mail.unionsystechnologies.com`.

### Configure DNS Records:
- **A record** → `mail.unionsystechnologies.com` → your static IP  
- **MX record** → `@` → `mail.unionsystechnologies.com`  
- **SPF record** →  
  ```
  v=spf1 mx ip4:<your_static_ip> -all
  ```
- **DKIM record** → Generated after server setup  
- **DMARC record** →  
  ```
  v=DMARC1; p=quarantine; rua=mailto:dmarc@unionsystechnologies.com
  ```

---

## 🛠️ Step 3: Install Mail Server Software
Install required packages:
```bash
apt install postfix dovecot-core dovecot-imapd opendkim opendkim-tools spamassassin fail2ban -y
```

- **Postfix** → Mail Transfer Agent (sending emails)
- **Dovecot** → For IMAP/POP (if receiving emails needed)
- **OpenDKIM** → DKIM signing
- **SpamAssassin** → Basic spam filtering
- **Fail2Ban** → Security protection
- **Let’s Encrypt** → TLS/SSL certificates for encryption

---

## 🛠️ Step 4: Bulk Mail Configuration
Edit `/etc/postfix/main.cf` to tune for bulk sending:
```bash
default_process_limit = 100
smtpd_recipient_limit = 100
smtpd_client_connection_count_limit = 50
```

Enable queueing (emails retry if delayed).

Setup bounce handling (invalid emails automatically managed).

---

## 🛠️ Step 5: Blasting Software
- Use **SendBlaster** and connect SMTP to:
  - Server: `mail.unionsystechnologies.com`
  - Port: 587 (STARTTLS)
  - Auth: Your mail server credentials

- Or install **Postal / Mailtrain** on server for advanced campaign management.

---

## 🛠️ Step 6: Warm-Up Strategy
- Start with ~200–300 emails/day.
- Gradually increase daily volume (500 → 1000 → 5000 → more).
- Test deliverability with Gmail, Yahoo, Outlook inboxes.

---

## 🛠️ Step 7: Monitoring & Reputation
- Monitor logs:
  ```bash
  tail -f /var/log/mail.log
  ```
- Check blacklist status regularly: [MXToolbox](https://mxtoolbox.com/blacklists.aspx)
- Keep system updated and watch server health.

---

## ✅ Key Notes
- With **16GB RAM + static IP**, this setup is fully sufficient for medium to large email campaigns.
- Deliverability depends heavily on correct **SPF, DKIM, DMARC, rDNS** setup and **gradual IP warm-up**.
- For safety, consider using a **subdomain** for blasting (e.g., `news.unionsystechnologies.com`) to protect your main domain reputation.

