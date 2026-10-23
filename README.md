# Phishing Email Analysis & Threat Assessment

Analysis and investigation of a simulated phishing email, from header forensics and IOC extraction through social-engineering breakdown and SOC response recommendations.

* **Incident ID:** INC-001
* **Severity:** Medium
* **Tools Used:** MXToolBox, VirusTotal, IP Lookup, Base64 Decoder, Email Header Analyzer

---

## Executive Summary

This project documents the end-to-end investigation of a sample phishing email, mimicking a Google account-lockout notice. The analysis covered email header forensics, sender authentication checks (SPF/DKIM/DMARC), URL and redirection-chain analysis, indicator of compromise (IOC) extraction, and a breakdown of the social-engineering tactics used to manipulate the recipient. The goal was to demonstrate a structured phishing triage process a SOC analyst would follow, from initial artifact collection to a final risk classification and recommended response.

---

## Email Artifacts

| Artifact | Finding |
|---|---|
| Display/From address | nooreply@qqnyoxisrnb.us |
| SMTP envelope sender | naldqxdfvvbfi@google-appleamazon.chelsea.uk.com.tountas.org |
| Sending IP | 89.252.128.143 — blacklisted (RATS Spam, Spamhaus ZEN) |
| Subject | "⚠️ Urgent Warning! Your account has been locked... Renew for free today!" |
| Embedded URL | `storage.googleapis.com/worklinks/evrtinggood.html#...` |
| Redirect domain | sidentin.edu.eu.org — flagged as phishing on VirusTotal |

The display "From" address and the SMTP envelope sender do not match, a classic sender-spoofing indicator that's invisible to the average recipient but immediately visible in full header analysis.

---

## Email Authentication Results

| Check | Result | Notes |
|---|---|---|
| SPF | Pass | Only validates the envelope domain, not the spoofed display address |
| DKIM | Not observed | No usable signature in headers |
| DMARC | Not observed | No usable result available |
| ARC | Present | ARC Seal header present |

SPF passing is a useful but limited signal here, it confirms the *envelope* domain was authorized to send, not that the visible sender identity is legitimate. This is a good example of why SPF alone is insufficient for phishing detection.

---

## Indicators of Compromise (IOC)

| Type | Indicator |
|---|---|
| Email address | nooreply@qqnyoxisrnb.us |
| Domain | qqnyoxisrnb.us |
| Malicious URL | hxxps://sidentin[.]edu[.]eu[.]org/ItRNotfLUKoScdFUvrXMRDlYmXNKnD |
| Sender IP | 89.252.128.143 |

---

## Social Engineering Tactics Identified

1. **Impersonation / Brand Spoofing** — mimicked Google branding and signed off as "Google Account Security Team" to borrow authority and lower skepticism
2. **Fabricated Urgency** — multiple conflicting deadlines ("within 24 hours", "expires in 1 hour", "deletion deadline: today") to force hasty action
3. **Fear Appeal / Loss Aversion** — repeated threats of permanent data loss ("ALL YOUR FILES... will be DELETED")
4. **False Incentive (Baiting)** — offered "FREE 100GB" as a relief path, incentivizing the click
5. **Visual Deception** — diamond symbols in the subject line, red warning banners, and a prominent CTA button designed to distract from generic greetings and spoofed headers

Breaking these down individually, rather than just saying "it looked suspicious," is the actual analytical skill this section demonstrates.

---

## URL & Redirection Analysis

The embedded link used Google-hosted infrastructure (`storage.googleapis.com`) as a first hop, a common technique to evade naive URL filtering by hiding behind a trusted domain. The link then redirected to `sidentin.edu.eu.org`, which VirusTotal flagged as phishing.

**Key takeaway:** hosting on legitimate infrastructure (Google Cloud Storage, in this case) does not mean the content or the sender is legitimate, this is worth calling out explicitly since it's a common point of confusion.

---

## Recommended SOC Response

**Immediate actions**
- Quarantine the message and block further delivery
- Block the malicious domain/URL
- Search environment-wide for the same sender/IOCs
- Determine whether any user clicked the link or submitted credentials
- Check endpoint/browser telemetry if interaction occurred
- Reset credentials and revoke active sessions if compromise is confirmed

**Preventive actions**
- Strengthen email filtering and phishing detection rules
- Enforce SPF, DKIM, and DMARC across the organization
- Implement URL scanning / sandboxing
- Conduct user awareness training
- Monitor for lookalike domains

---

## Conclusion

The evidence collected, sender/envelope mismatch, blacklisted sending IP, failed DKIM/DMARC verification, a VirusTotal-flagged redirect domain, and a textbook set of social-engineering tactics, strongly supports classifying this email as phishing. The investigation demonstrates a complete triage workflow: artifact collection, authentication verification, URL/redirection analysis, IOC extraction, and response recommendations

---

## Full Report

Full header dumps, MXToolBox blacklist results, and VirusTotal screenshots are available in: <a href= "https://drive.google.com/file/d/1zWxY2kiE_PCeUhVaJV0J13JgqI6xN09d/view?usp=drive_link">VIEW DOCUMENT</a>
