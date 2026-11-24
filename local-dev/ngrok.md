[← Back to index](../README.md)

# 🌐 ngrok: open a local site on a public URL

Gives a local site such as `http://helpdesk.test` a public **https** address. Use it to test webhooks or to open the site from a phone.

## ⚡ Quick reference

| Task | Command |
|---|---|
| ▶️ Start the tunnel | `ngrok http 80 --host-header=helpdesk.test` |
| ⏹️ Stop the tunnel | <kbd>Ctrl</kbd> + <kbd>C</kbd> |
| 🔍 See every request | open http://127.0.0.1:4040 |

## 1️⃣ One-time setup (new machine)

```bash
brew install ngrok
ngrok config add-authtoken <your-token>
```

Get the token from https://dashboard.ngrok.com/get-started/your-authtoken.

## 2️⃣ Start the tunnel

```bash
ngrok http 80 --host-header=helpdesk.test
```

Copy the https address from the `Forwarding` line. That address now opens the local site:

```text
Forwarding   https://a1b2-c3d4.ngrok-free.app -> http://localhost:80
```

For another site, change the name: `--host-header=mysite.test`.

> [!IMPORTANT]
> The address changes **every time ngrok restarts**. Update anything that uses it, such as a webhook callback URL.

> [!NOTE]
> **Why `--host-header`?** nginx picks the site by its host name. Without the flag the request arrives as `a1b2-c3d4.ngrok-free.app`, and nginx doesn't send it to `helpdesk.test`.

## 3️⃣ Inspect requests

Open **http://127.0.0.1:4040** to see every request through the tunnel, with headers and body. Use it to read or replay webhook payloads.

> [!TIP]
> When two tunnels run at once, the second one gets **:4041**. Find each tunnel's address on its `Forwarding` line.

## 🧩 Laravel: links point back to `http://site.test`

If the page opens but links, redirects or CSS still point at `http://helpdesk.test`, Laravel isn't trusting the tunnel. Trust every proxy locally (Laravel 10):

```php
// app/Http/Middleware/TrustProxies.php
protected function proxies()
{
    return app()->environment('local') ? '*' : $this->proxies;
}
```

Leave `APP_URL` as it is.
