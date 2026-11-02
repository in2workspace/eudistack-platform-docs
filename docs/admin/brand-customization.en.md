# Brand customization

This page is for whoever prepares your organization's look in EUDIStack: the brand or communications team, or the project lead. It explains what we can customize, what material we need and how we put it in place.

---

## What is customized

Your users see your brand in the interfaces they work with:

- **The Wallet app**, where people store and present their attestations.
- **The sign-in page**, where people identify themselves with their wallet.
- **The issuance portal**, where a person obtains their attestation.
- **The administration portal**, where your organization issues and manages attestations.
- **The proximity verifier**, to check attestations in person.
- **The emails** your users receive: activation code, attestation ready, incidents and revocation or expiry notices.

In all of them we apply your name, your colours, your logo and your favicon. In the Wallet app we also apply the installed app icon and the attestation card design.

---

## What you need to send us

### Logos and icons

| Item | Format | Size | Used for |
|---|---|---|---|
| Logo for coloured backgrounds | SVG (preferred) or PNG with a transparent background | PNG at least 500 px wide and under 150 KB. Landscape | Top bars in your primary colour |
| Logo for white backgrounds | SVG or PNG with a transparent background | Same as above | White headers |
| Favicon | SVG, or square PNG | 32 x 32 px and 180 x 180 px. Under 50 KB | Browser tab and mobile home-screen shortcut |
| App icon | Square PNG, no transparency | 512 x 512 px. Keep the artwork within the central 80 %: some phones crop the icon to a circle | Icon of the installed Wallet app |

If your logo has a single version that works on both white and colour, send just that one. We will tell you if it does not read well on some screen.

### Colours

Send each colour as a six-digit hexadecimal value, for example `#0F2B5B`.

| Colour | Required | Where it shows |
|---|---|---|
| Primary | Yes | Top bars and brand areas |
| Text on primary | Yes | Text and icons on top of the primary colour |
| Secondary | Yes | Brand details. Also buttons and icons if your primary is white or near-white |
| Text on secondary | Yes | Text on top of the secondary colour |
| Attestation card: start colour, end colour and text | No | Card the user sees in their Wallet app. It is a gradient between the two colours |
| Access screens: start colour and end colour | No | Background of the sign-in page and of the app's access screens |

If you do not send the optional ones, we derive them from the primary and secondary colours.

!!! tip "Contrast"
    Everyone must be able to read the interfaces. Check each pair of background and text colour with a WCAG contrast checker. We ask for at least 4.5:1 for text and 3:1 for icons, borders and graphic elements. A white or very light primary colour works, but buttons then take the secondary colour: check that the secondary also contrasts with white.

### Languages

Choose the languages your interfaces will offer and which one is the default.

- Spanish and English are available in every interface.
- Catalan is available in the Wallet app, the sign-in page, the administration portal and the proximity verifier. Not in the issuance portal.
- Emails are sent in a single language per organization: Spanish or English.

Each person sees the interfaces in their browser's language if your organization offers it. Otherwise, in the default language.

### Texts and links

| Item | Format | Where it shows |
|---|---|---|
| Organization name | Text. 20 characters or fewer recommended | Page titles, installed app name and email subjects |
| Support page | `https` address | Help links in the Wallet app, the sign-in page and the emails |
| Support email | Email address | Wallet app and issuance portal |
| Support phone, hours and response time | Text | Issuance portal |
| Help centre | `https` address (optional) | Wallet app and emails |
| Legal notice, privacy policy, terms of use and accessibility statement | `https` address | Issuance portal |
| Your own texts for each step of the issuance portal | Text per language (optional) | Issuance portal |

### Emails

| Item | Format | Size |
|---|---|---|
| Email logo | PNG with a transparent background | Shown at 54 px high. Send it at 300 px high at most |
| Header image (optional) | PNG | **Exactly 640 x 190 px.** Some email programs tile the image if it has another size |
| Social networks (optional) | Address of your LinkedIn or GitHub profile | — |

### Applications that request presentations

If your applications ask people to present an attestation, the Wallet app shows who is asking. For each application, send us its name, its logo, its website and, if it has them, its privacy policy and terms of use.

### Sign-in page header and footer (optional)

You can embed your website's header and footer in the sign-in page. Send us the HTML. We accept structure, `https` links, images, text and SVG. We do not accept scripts, style sheets, forms or iframes: they are removed when the page loads.

---

## What cannot be customized today

- **The typeface.** All interfaces use the product typeface.
- **Status colours.** Error, success and warning have fixed colours so they are always recognized the same way.
- **The colour of some buttons.** When a brand colour does not guarantee good legibility, some screens use a neutral blue for actions.
- **Email layout and texts.** We customize the logo, header image, links and name. The rest is shared.
- **The email sender.** Emails are sent from a platform address, not from your domain.
- **Texts of the Wallet app, the sign-in page and the administration portal.** Only the issuance portal accepts your own texts.
- **Languages other than those listed.**

If you need any of these, tell us. We study it as a separate project, with its own timeline.

---

## How we put it in place

1. You send us the material in the checklist.
2. We review it: formats, sizes and contrast. If something does not work, we send it back with what needs to change.
3. We configure it in the pre-production environment.
4. You review it in pre-production and give us your approval.
5. We move it to production.

**Lead times**, counted from when we have all the correct material. We confirm the exact time when we receive it:

- Brand of a new organization: part of its onboarding on the platform; it follows the onboarding plan.
- Once published, the change reaches your users within a few minutes. The Wallet app shows it the next time it opens with a connection.

---

## Checklist

Copy this list and send it back complete with the files.

```text
Organization
[ ] Trade name (20 characters or fewer)
[ ] Contact person to review the brand in pre-production

Logos and icons
[ ] Logo for coloured backgrounds (SVG or transparent PNG, 500 px wide or more)
[ ] Logo for white backgrounds (SVG or transparent PNG)
[ ] Favicon (SVG, or PNG at 32x32 and 180x180)
[ ] App icon (PNG 512x512, artwork within the central 80 %)

Colours (hexadecimal #RRGGBB)
[ ] Primary and text on primary
[ ] Secondary and text on secondary
[ ] Attestation card: start, end and text (optional)
[ ] Access screens: start and end (optional)
[ ] Contrast checked: 4.5:1 text, 3:1 icons and borders

Languages
[ ] Interface languages and default language
[ ] Email language (Spanish or English)

Support and legal
[ ] Support page (https)
[ ] Support email
[ ] Phone, hours and response time
[ ] Help centre (https, optional)
[ ] Legal notice, privacy, terms and accessibility (https)

Emails
[ ] Email logo (transparent PNG)
[ ] Header image 640x190 px (optional)
[ ] Social networks (optional)

Optional
[ ] Your own issuance portal texts, per language
[ ] Applications that request presentations: name, logo, website, privacy and terms
[ ] Sign-in page header and footer in HTML
```

Questions? [Contact support](../support.en.md).
