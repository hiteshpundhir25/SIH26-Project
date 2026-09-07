# Sahakari Seva — Multilingual Localization System (i18n)

## 1. Overview & Cultural Significance

In India's cooperative gig economy, over 70% of skilled trade professionals operate comfortably in **Hindi (हिन्दी)** and regional languages rather than English. Sahakari Seva implements comprehensive first-class bilingual support across both its mobile and web applications.

- **Supported Locales**: English (`en`) and Hindi (`hi` / देवनागरी).
- **Engine**: `i18next` (v24) + `react-i18next` (v15).
- **Switching Latency**: $\le 50\text{ms}$ with zero application restarts or page reloads required.

---

## 2. Directory Structure & Catalogs

```
mobile/src/i18n/
├── index.ts      # i18next initialization & persistence hooks
├── en.json       # Complete English translation catalog
└── hi.json       # Complete Hindi (देवनागरी) translation catalog
```

### Namespace Organization:
Both catalogs feature identical JSON key topologies:
1. `app_name`: "सहकारी सेवा" / "Sahakari Seva"
2. `tagline`: Cooperative gig services federation description
3. `roles`: Customer, Worker, Federation Admin
4. `nav`: Home, Search, Map, Bookings, Jobs, Location, Forecast, Allocation
5. `services`: Titles, subtitles, and descriptions for all 8 trades
6. `matching`: Distance indicators, match percentages, verification badges
7. `status`: Available, Busy, Offline, In Progress, Completed
8. `admin`: Workforce allocation, AI forecast metrics, mobilization actions

---

## 3. Database Schema Localization

In addition to client-side UI labels, database models retain bilingual field representations (`name_hi`, `description_hi`):

```sql
SELECT 
  id, 
  name AS name_en, 
  name_hi, 
  description AS desc_en, 
  description_hi 
FROM public.service_categories;
```

### Example Trade Category Mapping:
| English Name | Hindi (देवनागरी) | Description Snippet (Hindi) |
|---|---|---|
| **Electrical** | विद्युत सेवाएं (इलेक्ट्रीशियन) | वायरिंग मरम्मत, शॉर्ट-सर्किट, पंखा व स्विच फिटिंग। |
| **Plumbing** | नलसाजी सेवाएं (प्लम्बर) | पाइप लीकेज, नल मरम्मत, ड्रेनेज ब्लॉकेज व मोटर फिटिंग। |
| **Carpentry** | बढ़ईगीरी सेवाएं (बढ़ई) | फर्नीचर मरम्मत, ताले बदलना, अलमारी व दरवाजा फिटिंग। |
| **Painting** | पुताई और पेंटिंग | घर की पुताई, वॉटरप्रूफिंग, डिस्टेंपर और टेक्सचर पेंट। |
| **Cleaning & Sanitization** | सफाई और स्वच्छता | डीप होम क्लीनिंग, सोफा शैम्पू, किचन व बाथरूम डीग्रीजिंग। |
| **Gardening** | बागवानी सेवाएं | लॉन कटाई, पौधों की छंटाई, जैविक खाद, टेरेस गार्डन मेंटेनेंस। |
| **Appliance Repair** | घरेलू उपकरण मरम्मत | वॉशिंग मशीन, माइक्रोवेव, फ्रिज, गीज़र व मिक्सर मरम्मत। |
| **Masonry & Civil** | चिनाई व मरम्मत कार्य | फर्श टाइल मरम्मत, प्लास्टर, दीवार दरारें और छत वाटरप्रूफिंग। |

---

## 4. Language Selector Component

Both Mobile (`mobile/src/components/common/LanguageModal.tsx`) and Web (`src/components/layout/Navbar.tsx`) expose an accessible language trigger:

```tsx
export const LanguageModal: React.FC<LanguageModalProps> = ({ visible, onClose }) => {
  const { i18n } = useTranslation();

  const changeLanguage = (lang: 'en' | 'hi') => {
    i18n.changeLanguage(lang);
    onClose();
  };
  ...
}
```

- **Persistence**: Remembers user choice across app sessions using device local storage.
- **Fallbacks**: Any untranslated key automatically falls back to English to guarantee zero missing text strings.
