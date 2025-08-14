# Analyze-Email-
Analyze Emails By a Header and Security Protocols and Authentications.

That’s exactly where SPF, DKIM, and DMARC results live.

Here’s how to **analyze an email from “Show Original”**:

---

## **1. Open “Show Original”**

* **Gmail:**

  * Open the suspicious email
  * Click the three dots **⋮** (More) on the top right
  * Select **Show Original**
  * A new tab opens with headers + Gmail’s SPF/DKIM/DMARC verdict.
* **Outlook (Web):**

  * Open email → click **…** (More actions) → **View message source**
* **Desktop clients:** Usually "View source" or "View headers."

---

## **2. Look for Authentication Results**

Search for a section like:

```
Authentication-Results: mx.google.com;
       spf=pass (google.com: domain of sender@example.com designates 203.0.113.45 as permitted sender)
       dkim=pass header.d=example.com header.s=selector1 header.b=abc123;
       dmarc=pass (p=REJECT sp=NONE dis=NONE) header.from=example.com
```

**Meaning:**

* **spf=pass** → Sending IP is authorized for this domain.
* **dkim=pass** → DKIM signature verified.
* **dmarc=pass** → SPF/DKIM are aligned with the “From” domain.

---

## **3. Check “Received” Lines**

These show the path the email took.
Example:

```
Received: from mail.example.com (mail.example.com. [203.0.113.45])
        by mx.google.com with ESMTPS id abc123
```

* The **first “Received” from the top** is where your mail server got it.
* The **bottom-most “Received”** is the original sending server.
* Compare the sending IP/domain with the supposed “From” domain.

---

## **4. Inspect the DKIM Signature**

Example:

```
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
    d=example.com; s=selector1;
    h=from:subject:date:message-id;
    bh=abc123hash;
    b=longsignature...
```

* `d=` → Domain that signed the email.
* If `d=` is **different** from the visible “From” domain and not a trusted 3rd party (e.g., Mailchimp), it’s suspicious.

---

## **5. Check DMARC Policy**

You can manually check with:

```
dig TXT _dmarc.example.com
```

Example record:

```
v=DMARC1; p=reject; rua=mailto:dmarc-reports@example.com
```

If no record → spoofing is easier.

---

## **6. Red Flags to Spot Quickly**

* SPF = **fail** or **softfail** for a trusted brand.
* DKIM **missing** or signed by an unrelated domain.
* DMARC **fail** or **none** policy.
* “From” domain doesn’t match the return-path or DKIM `d=` domain.
* Received IP from suspicious hosting or unrelated country.

---


