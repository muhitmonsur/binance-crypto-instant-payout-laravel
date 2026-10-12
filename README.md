[![Latest Stable Version](https://poser.pugx.org/payerurl/binance-and-crypto-checkout/v/stable)](https://packagist.org/packages/payerurl/binance-and-crypto-checkout)
[![Total Downloads](https://poser.pugx.org/payerurl/binance-and-crypto-checkout/downloads)](https://packagist.org/packages/payerurl/binance-and-crypto-checkout)
[![License](https://poser.pugx.org/payerurl/binance-and-crypto-checkout/license)](https://packagist.org/packages/payerurl/binance-and-crypto-checkout)
# 🪙 Crypto gateway || Binance API (no merchant account needed) || all instant payout
Laravel SDK to connecting payerurl API . 

> By using this API Receive customer payments directly and instantly in your crypto wallet. Accept BTC, ETH, USDT, and USDC from customers across all major networks.
> Accept payment using your binance regular account QR code payment (C2C).

**Powered by [PayerURL](https://payerurl.com)** — the direct-to-wallet crypto payment processor for Laravel developers.

🔴 **[LIVE DEMO](https://laravel.payerurl.com/)** | 🔑 **[Get API Key](https://dash.payerurl.com)** | 💬 **[Telegram Support](https://t.me/Payerurl)**

---
## Overview

This payment gateway lets customers pay directly on your store's checkout page. Once paid, the order automatically confirms via instant webhooks. Store owners can view complete transaction records in their PayerURL dashboard. PayerURL is safe, secure, and uses real-time exchange rates.

Depending on your PayerURL settings, your checkout offers:

- Popular Cryptos: Accepts Bitcoin, Ethereum, USDT, and USDC.
- Major Networks: Works on TRON (TRC20), Ethereum (ERC20), Binance Smart Chain (BEP20), and Bitcoin networks.
- Binance QR Code: Customers can pay by scanning a QR code with a personal Binance account—no merchant account needed.
- Global Currencies: Supports 169+ local currencies with automatic, live conversion to crypto.
- Card Payments: Options to buy crypto and pay instantly using credit cards, debit cards, Google Pay, or Apple Pay.
- New Address Per Order: Creates a unique receiving address for every order using XPUB to keep payments private and secure.

---

## ✅ Why Developers Choose This Package

| Feature | Detail |
|---|---|
| 🏦 **No merchant account needed** | Payments go directly to your crypto wallet |
| 🌍 **169+ fiat currencies** | USD, EUR, GBP, CAD and more — converted at live rates |
| ⚡ **10-minute integration** | Simple composer install, clear docs, copy-paste code |
| 🔒 **No KYC for basic accounts** | Start accepting payments without identity verification |
| 📱 **Binance QR Code payments** | Customers scan and pay without leaving your app |
| 💸 **Zero hidden fees** | No network surcharges or platform fees on the plugin |
| 🛠️ **Laravel 8, 9, 10, 11 ready** | Works with any modern Laravel version |

---

## 📦 Installation

```bash
composer require payerurl/binance-and-crypto-checkout
```

---

## ⚙️ Publish Configuration

```bash
php artisan vendor:publish \
  --provider="Payerurl\Providers\AppServiceProvider" \
  --tag=config
```

---

## 🔑 Get Your API Key (Free)

1. Sign up at **[dash.payerurl.com](https://dash.payerurl.com)**
2. Go to **Dashboard → Get API Credentials**
3. Copy your **Public Key** and **Secret Key**

> 👉 Registration is free and takes under 2 minutes. No credit card required.

---

## 🔑 Environment Configuration

Add your API credentials to `.env`:

```env
PAYERURL_PUBLIC_KEY="your_public_key"
PAYERURL_SECRET_KEY="your_secret_key"
```

Get your API keys from: https://dash.payerurl.com/profile/get-api-credentials

---

## 🚀 Quick Start (Controller Integration)

```php
use Payerurl\Payerurl;

public function pay()
{
    $invoiceId = 'LRB-' . time();
    $amount    = 1000; // $1000.00
    $currency  = 'usd';

    // $orderItems Is optional
    $orderItems = [
        [
            'name' => str_replace(' ', '_', 'Order item name'), // Replace spaces with '_' , no space allowed
            'qty' => 1,
            'price' => '123',
        ]
    ];

    $data = [
        'first_name'   => 'Alice',
        'last_name'    => 'Smith',
        'email'        => 'alice@example.com',
        'redirect_url' => route('payment.success'),
        'cancel_url'   => route('cart'),
    ];

    $response = Payerurl::payment($invoiceId, $amount, $currency, $data, $orderItems);

    if ($response['status']) {
        return redirect()->away($response['redirectUrl']);
    }

    return back()->with('error', $response['message']);
}
```

Send the customer to `$response['redirectUrl']` — they pay with crypto, you receive it instantly in your wallet.

---

## 🌐 Supported Cryptocurrencies & Networks

| Currency | Networks |
|---|---|
| **USDT** | TRC20 (Tron), ERC20 (Ethereum), BEP20 (BSC) |
| **USDC** | ERC20 (Ethereum), BEP20 (BSC) |
| **Bitcoin (BTC)** | Bitcoin Network |
| **Ethereum (ETH)** | ERC20 |
| **Binance Pay** | Binance QR Code |

---

## 💳 Payment Integration

### 📌 Function Signature

```php
payment($invoiceId, $amount, $currency = 'usd', $data)
```

### ✅ Required Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `$invoiceId` | string | Yes | Unique Order ID |
| `$amount` | int | Yes | Amount in smallest unit (e.g., cents) |
| `$currency` | string | No | Default: `usd` |
| `$data` | array | Yes | Customer & URL information |

### 📦 `$data` Array Structure

```php
$data = [
    'first_name'   => 'John',
    'last_name'    => 'Doe',
    'email'        => 'john@example.com',
    'redirect_url' => 'https://yourdomain.com/payment-success',
    'cancel_url'   => 'https://yourdomain.com/checkout',
];
```

> The package registers a webhook route automatically (`POST /payerurl/notify`, route name `payerurl.notify`). You do not need to pass `notify_url` in `$data`.

---

## 📲 How the Binance QR Payment Works

1. Your Laravel app calls the API and gets a **payment URL**
2. Customer is redirected to a secure hosted checkout page
3. Customer **scans the QR code** with their Binance app
4. Payment is confirmed and funds land **directly in your wallet**
5. Your `notify_url` receives a webhook with the order status update

No bank accounts. No intermediaries. No waiting.

---

## 🔔 Webhook (Payment Notify)

After payment, Payerurl POSTs to the package endpoint automatically:

- **URL:** `POST {APP_URL}/payerurl/notify`
- **Route name:** `payerurl.notify`

Verification (public key, signature, order fields) is handled by the package. On successful payment (`status_code` 200), it fires `Payerurl\Events\PaymentNotifySuccess`.

Listen in `app/Providers/EventServiceProvider.php`:

```php
use Payerurl\Events\PaymentNotifySuccess;

protected $listen = [
    PaymentNotifySuccess::class => [
        \App\Listeners\UpdateOrderOnPayerurlPayment::class,
    ],
];
```

Example listener:

```php
public function handle(PaymentNotifySuccess $event): void
{
    $orderId = $event->payload['order_id'];
    // Update your order status here
}
```

> If your app uses the `web` middleware group on this route, exclude CSRF for the webhook in `bootstrap/app.php` or `VerifyCsrfMiddleware` (`payerurl/notify`).

Optional logging: set `PAYERURL_LOG_NOTIFICATIONS=true` in `.env`.

---

## 📊 Full Payment Flow Diagram

```
Your Laravel App → PayerURL API → Checkout Page → Customer Pays (Binance/Crypto)
                                                            ↓
Your Wallet ← Funds (instant) ← Payment Verified ← Blockchain
                                                            ↓
              Your notify_url ← Webhook (order status update)
```

---

## 🔄 API Response

### ✅ Successful Payment Request

```php
[
    'status'      => true,
    'redirectUrl' => 'https://dash.payerurl.com/payment/WP112XXXXX'
]
```

### ❌ Error Response

```php
[
    'status'  => false,
    'message' => 'Something went wrong. Please try again.'
]
```

---

HOW THE PLUGIN WORKS
ABC Crypto Checkout — powered by Payerurl — is a non-custodial cryptocurrency payment gateway that connects WooCommerce directly to your crypto wallets. Whether you accept Bitcoin (BTC), Ethereum (ETH), USDT TRC20, USDT ERC20, USDC ERC20, Binance Pay, TON, or other major cryptocurrencies, payments land in your wallet instantly with zero middlemen. Now with XPUB (Extended Public Key) support, you can auto-rotate through unlimited receiving addresses — ideal for high-volume stores, crypto exchanges, and enterprise merchants.

ABC Crypto Checkout converts any fiat currency (USD, EUR, GBP, JPY, CAD, AUD, and 160+ more) to the customer’s chosen cryptocurrency using a live exchange rate. Once the blockchain payment or Binance Pay transaction is confirmed, funds are credited instantly to your wallet and your WooCommerce order status is updated to “Processing” automatically via API webhook — no manual steps required.

SUPPORTED CRYPTOCURRENCIES
🔸 Bitcoin (BTC) — The world’s #1 cryptocurrency
🔸 Ethereum (ETH ERC20) — The leading smart contract blockchain
🔸 USDT TRC20 — Tether on TRON (ultra-low fees)
🔸 USDT ERC20 — Tether on Ethereum
🔸 USDC ERC20 — USD Coin, the trusted stablecoin
🔸 Binance Pay — Instant Binance account payments
🔸 Binance QR Code — Contactless Binance scan-to-pay
🔸 TON Coin — The Open Network blockchain
🔸 XPUB Wallet Addresses — Auto-rotating HD wallet addresses for unlimited transactions

KEY FEATURES
🔸 Available in 5 languages: English, Spanish, Russian, Arabic, and Portuguese.
🔸 XPUB Unlimited Wallet Integration — Auto-generate unique addresses per order for mass-volume transactions
🔸 10+ Crypto Networks — Bitcoin BTC, Ethereum ETH, USDT TRC20, USDT ERC20, USDC ERC20, Binance Pay, Binance QR, TON, and more
🔸 169+ Fiat Currencies — USD, EUR, GBP, JPY, CAD, AUD, INR, and more with real-time exchange rates
🔸 Non-Custodial & Trustless — Funds go straight to your wallet; we never hold your crypto
🔸 No KYC Required — No identity verification for the Basic (free) plan
🔸 No Bank Account Needed — Accept crypto payments without any traditional banking setup
🔸 100% Free & Open Source Plugin — No hidden fees on the plugin itself
🔸 WooCommerce Blocks Compatible — Fully supports modern WooCommerce checkout blocks
🔸 HPOS (High-Performance Order Storage) Compatible — Ready for WooCommerce’s latest order management system
🔸 Pay Later Option — Flexible payment timing for customers
🔸 Auto Partial Payment Handling — Order marked as Processing when ≥90% of payment is received
🔸 Discount Feature — Offer crypto-specific discounts to incentivize on-chain payments
🔸 Live Exchange Rate — Real-time crypto/fiat conversion at checkout
🔸 Buy Crypto with Card or Bank — Customers can pay via Visa, Mastercard, credit/debit card, or bank transfer; auto-converted to crypto for the merchant
🔸 Easy API Key Setup — One-click credential check and save
---

## 🖼 Screenshots

![Screenshot 1](https://raw.githubusercontent.com/muhitmonsur/assets/refs/heads/main/screenshot-1.png)
![Screenshot 2](https://raw.githubusercontent.com/muhitmonsur/assets/refs/heads/main/screenshot-2.png)
![Screenshot 4](https://raw.githubusercontent.com/muhitmonsur/assets/refs/heads/main/screenshot-4.png)
![Screenshot 5](https://raw.githubusercontent.com/muhitmonsur/assets/refs/heads/main/screenshot-5.png)
![Screenshot 6](https://raw.githubusercontent.com/muhitmonsur/assets/refs/heads/main/screenshot-6.png)
![Screenshot 7](https://raw.githubusercontent.com/muhitmonsur/assets/refs/heads/main/screenshot-7.png)
![Screenshot 8](https://raw.githubusercontent.com/muhitmonsur/assets/refs/heads/main/screenshot-8.png)

---

## 📬 Support

| Channel | Link |
|---|---|
| 💬 Telegram | [t.me/Payerurl](https://t.me/Payerurl) |
| 🌐 Website | [payerurl.com](https://payerurl.com) |
| 📧 Email | support@payerurl.com |
| 📊 Dashboard | [dash.payerurl.com](https://dash.payerurl.com) |
| 🔴 Live Demo | [payerurl.com](https://laravel.payerurl.com/) |

---

## 🧾 License

MIT License — free for personal and commercial use.

---

## 🏷️ Keywords

`crypto payment` `bitcoin payment laravel` `binance payment gateway` `usdt payment laravel` `usdc payment laravel` `crypto checkout laravel` `binance qr code laravel`  `binance pay api` `laravel crypto gateway` `best crypto payment gateway` `fiat to crypto payment gateway` `cryptocurrency payment gateway`
