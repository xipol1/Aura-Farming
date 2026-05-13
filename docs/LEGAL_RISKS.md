# Legal Risks

## Read this before adding any image or external content.

This document is not legal advice. It is a working risk register based on practical reading of EU/India/US IP and content law. When in doubt, consult a lawyer or omit the content.

---

## Copyright on images

### The golden rule

**Attribution does not equal permission.** Putting "source: Reuters" under a photo does not exempt you from copyright infringement. Attribution is a *condition of certain licenses* (CC-BY), not a substitute for licensing.

### Allowed image sources

1. **Public domain works** verified via [Wikipedia Public Domain](https://en.wikipedia.org/wiki/Public_domain) or [Wikimedia Commons](https://commons.wikimedia.org/) with explicit "Public Domain" or "CC0" tags.
2. **Creative Commons licensed images** matching our use:
   - CC0 / Public Domain Dedication: any use, no attribution required.
   - CC-BY: any use *if* attributed.
   - CC-BY-SA: any use *if* attributed *and* derivative work is licensed alike (avoid — viral license clause).
   - CC-BY-NC: *not allowed* if we ever monetize.
3. **Original work we create** (illustrations, generated SVGs, photography we take).
4. **Embeds via official platform APIs** (Twitter oEmbed, Instagram embeds, YouTube iframes) — these come with the platform's terms attached and are generally safe when used as intended.

### Forbidden image sources

- Photo wire services (Getty, AP, Reuters, AFP, EPA): always copyrighted, aggressive enforcement.
- Press photos and editorial photography off general web search.
- Stock photo sites' watermarked previews.
- Screen captures from copyrighted films, TV shows, video games, anime.
- Personal photos of public figures (right of image / personality rights, especially strong in EU and India).
- AI-generated images of real recognizable people (right of image still applies regardless of how the image was made).

### Our chosen approach for Aura Off

**No third-party images in the product.** Every visual element is:
1. **Text label** (the item name as typography — fully legal, describing a public fact).
2. **Original SVG illustration** in our unified meme-sticker style (8 illustrations for 8 tags, one each).
3. **No item-specific imagery.** "Messi raising the World Cup" appears as text, not as a photo of Messi.

This eliminates 99% of copyright exposure with minimal aesthetic cost. The meme value lives in the text + the score, not in a photograph.

---

## Right of image / personality rights

Separate from copyright. Protects living persons (and recently deceased, in some jurisdictions) from commercial use of their identity without consent.

### Jurisdictional notes

- **Spain (where founder is based)**: LO 1/1982 strong protection for image, honor, intimacy. Commercial use of a person's image without consent is actionable.
- **India**: developing case law on personality rights (Anil Kapoor case 2023 explicitly protected name, voice, image). Bollywood and cricket figures aggressively litigious.
- **USA**: state-by-state. California particularly strong (CCPA + right of publicity).

### Our exposure

Text labels referring to public moments are protected speech (factual reference). "Messi raising the World Cup" is a description of a public event, not a commercial use of Messi's image.

**Score assignments are opinion-based satire**, which is also protected. "Andrew Tate giving life advice: -47,200 aura" is a satirical opinion.

**Lines we do not cross**:
- No factual claims about real people that could be false (e.g. "X is a criminal", "Y stole money").
- No item that ties a real named person to a private fact (illness, sexual orientation, family disputes).
- No use of trademarked logos, character likenesses, brand assets without permission.

---

## Defamation

Truth and opinion are defenses. We stay safe by:

1. **Items describe events, not characterize people.** "Steve Buscemi 'fellow kids' scene" not "Steve Buscemi is cringe".
2. **Aura scores are satire**, not factual statements.
3. **Negative scores on identifiable people are kept satirical and bounded.** We do not run "lowest aura humans" leaderboards on real people.
4. **Items mentioning living public figures are reviewed for tone.** When in doubt, generalize ("LinkedIn hustle post archetype") rather than name.

---

## India: Digital Personal Data Protection Act (DPDPA 2023)

In force since 2025. Key obligations relevant to us:

1. **Children under 18**: explicit verifiable parental consent required for processing personal data of minors. We avoid this regime entirely by:
   - **Minimum age 18 in Terms of Service.** DPDPA defines a child as <18, so we set our floor at the statutory adult age. No edge cases, no parental consent flow.
   - **Age-gate at first interaction**: a modal asks for date of birth on first visit. Stored locally. If under 18, the app shows a "come back later" screen and does not write to Supabase.
2. **Data minimization**: we only store device fingerprint, streak counts, share events. No email, no real name, no location, no profile data.
3. **Right to erasure**: provide a "delete my data" link in the footer that issues a single Supabase delete by device_fingerprint. Implement before launch.
4. **Data Protection Officer**: not required at our scale.
5. **Cross-border data transfer**: Supabase EU region is acceptable. India does not currently block EU-hosted data for non-sensitive personal data.

---

## India: IT Rules 2021 + Intermediary Guidelines

Apply because we host user content (share events). Practically:

1. **Grievance officer**: required for "significant social media intermediaries" (>5M Indian users). We are not significant. When we cross the threshold, appoint one.
2. **Content takedown SLA**: 36 hours for objectionable content. We don't host user-generated content publicly (no submissions, no feed). Share cards are private until the user shares them. This dramatically reduces our exposure.
3. **Traceability**: government can request user identification. With anonymous-only auth, we have nothing to trace beyond a device fingerprint, which is acceptable for our risk profile.

---

## EU: GDPR

Applies because the founder is EU-based and we may have EU users.

1. **Legal basis for processing**: legitimate interest (running a game), and we ask for no personal data.
2. **Cookies**: we use one cookie (deviceId) for game state. This is strictly necessary cookie, no consent banner required under PECR. We do not use tracking cookies. PostHog is configured in cookieless mode.
3. **Privacy policy**: required. Drafted before launch.
4. **Data Processing Agreement** with Supabase: signed automatically on Supabase Free plan terms.
5. **Right to access / erasure**: same mechanism as India — delete by device_fingerprint endpoint.

---

## Trademarks

### "Aura Off" name

Before launching, check:

- **EUIPO** (EU): [search.euipo.europa.eu](https://search.euipo.europa.eu/) for "Aura Off", "AuraOff", "AURA OFF" in classes 9 (software), 41 (entertainment), 42 (SaaS).
- **USPTO** (USA): [tmsearch.uspto.gov](https://tmsearch.uspto.gov/).
- **India IP Office**: [ipindia.gov.in](https://ipindia.gov.in/) tm search.

If any conflict in our classes, pivot the name immediately. Cheaper to rename pre-launch than to defend.

We do not need to register the trademark for v1. Wait until 6 months of stable usage, then evaluate.

---

## App store policies (if/when we wrap natively)

- **Apple**: avoid presenting the game as containing accurate "aura scores" — describe it as satirical entertainment. Avoid health/medical claims.
- **Google Play**: similar. Plus, all in-app purchases must use Play Billing once in-store.
- **Both**: parental controls for users under 18 are stricter. Stick to web for v1.

---

## Risk register (top 10)

| # | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| 1 | Wire service copyright claim on an image we host | Low (we host none) | Critical | No third-party images ever |
| 2 | Personality rights complaint from a named living figure | Low | Medium | Text-only references, satirical tone, escalation path defined |
| 3 | DPDPA fine over a minor data leak | Low | High | Age gate, minimal data, EU hosting |
| 4 | Trademark conflict with name "Aura Off" | Medium | Medium | Pre-launch search, rename if needed |
| 5 | Sass line judged as harassment | Low | Low | Banned word list, no identity attacks, review banks pre-launch |
| 6 | Defamation claim over a satirical score | Very low | Medium | Score is satire, no false fact claims |
| 7 | User submits abusive content via share text (not currently possible) | N/A | — | No UGC in v1 |
| 8 | Government takedown order (India) | Very low | High | Comply with 36h SLA, geofence if needed |
| 9 | Children's data complaint | Low | High | Age gate, no PII collected |
| 10 | A competitor sues for trade dress | Very low | Low | Visual identity is original, brutalist |

---

## Escalation path

If we receive any legal notice:

1. **Do not respond immediately.** Acknowledge receipt only if required.
2. **Take down the contested content within 24h** if it's clearly disputable, while we figure out the rest. Better to remove than fight at our scale.
3. **Contact a lawyer.** For Spain-based, the founder's network. For India / international, retain on retainer once revenue > 5k€/month. Until then, free legal clinics (RootsOf, EFF for tech) for direction.
4. **Document everything.** Keep the original notice, our internal decision, the action taken. Date-stamped.
5. **Never make public statements** about the notice without legal review.

---

## Pre-launch legal checklist

- [ ] Trademark search complete (EUIPO + USPTO + India)
- [ ] Privacy policy written and published at `/privacy`
- [ ] Terms of service written and published at `/terms`
- [ ] Age gate implemented and tested
- [ ] Delete-my-data endpoint implemented and tested
- [ ] Content review: 100% of items.json reviewed for defamation / personality rights
- [ ] Content review: 100% of sass_responses.json reviewed for banned content
- [ ] Cookie banner: confirmed not required (strictly necessary only)
- [ ] DPDPA compliance documented
- [ ] No third-party imagery in repo or production
- [ ] Domain registered in founder's name (not a shell entity for v1)
