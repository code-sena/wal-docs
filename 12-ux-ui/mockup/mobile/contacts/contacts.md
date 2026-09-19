## Trusted contacts

**Route:** `/contacts` — BottomNav tab **CONTACTOS**
**Access:** USER
**Implementation status:** Implemented — list of trusted contacts with search, per-contact quick actions, edit/delete and an add FAB.

### Objective
Manage the people who will receive the user's location and alerts, and let her reach any of them instantly by call, WhatsApp or location share.

### Structure (top to bottom)
1. Hero header: "Mis Contactos de Confianza" + subtitle "Estas personas recibirán tu ubicación en caso de emergencia." + illustration on the right
2. Section row: 👥 "Tus contactos (1)" with the live count
3. Search field "Buscar por nombre o teléfono..."
4. **Contact card** (one per contact):
   - Circular avatar with the initial and a presence dot (green = available)
   - Name in `1.1rem 700` + relationship pill badge (e.g. 💗 "Hermana")
   - 📞 phone number · 🟢 "Disponible"
   - ✏️ edit and 🗑️ delete icon buttons at the top right
   - Divider, then a 3-action row: 📞 "Llamar" · 💬 "WhatsApp" (green tile) · 📍 "Ubicación"
5. **Empty-slot card:** dashed outline, "¿Quieres agregar más?" with an inline add button
6. Floating action button (➕👤) at the bottom right
7. BottomNav with CONTACTOS active

### Form fields (add / edit contact sheet)
| Field | Type | Required | Validation |
|-------|------|-----------|------------|
| Nombre | `text` | ✅ | Min. 2 characters |
| Teléfono | `tel` | ✅ | 10 digits (CO); unique in the list |
| Relación | `select` | ✅ | Madre · Padre · Hermana · Amiga · Pareja · Otro |
| Contacto prioritario | `switch` | ❌ | Called first on an urgent alert |

### Components used
- Hero header with illustration
- Search field
- Contact card with avatar, badge and a quick-action row
- Dashed empty-slot card
- FAB (add contact) + bottom sheet form
- Danger confirmation dialog for deletion

### States
- **loading:** Two skeleton contact cards
- **ready:** Cards rendered, count updated
- **searching:** The list filters as the user types
- **no-results:** "No encontramos contactos con esa búsqueda"
- **empty:** Only the dashed card plus the explanatory text
- **max-reached:** The FAB is disabled with the note "Máximo de contactos alcanzado"
- **editing:** Bottom sheet pre-filled with the contact data
- **deleting:** Danger dialog "¿Eliminar a [nombre] de tus contactos?"
- **unavailable:** Presence dot in gray with the label "No disponible"

### Constraints
- **Small phones (<360dp):** The quick-action labels shrink; the icons keep their size
- **Accessibility:** Each card is a group with `aria-label="Contacto [nombre], [relación], [estado]"`; icon-only buttons carry descriptive labels; presence stated as text, not only as a dot; the FAB is at least 56dp
- **Dark mode:** Card `#1c1c22`; the WhatsApp tile keeps its brand green

### Prompt ready for Google Stitch
> Design the Trusted Contacts screen of the AlertaMujer mobile app for Android. At the top, a purple gradient hero block with rounded bottom corners containing the two-line title "Mis Contactos de Confianza" in `1.5rem 700 #ffffff`, the subtitle "Estas personas recibirán tu ubicación en caso de emergencia." in `0.85rem rgba(255,255,255,0.85)` and a small illustration of a shield with people on the right. Below, on a `#f6f1fb` background: a row with a people icon and the bold label "Tus contactos (1)"; a white pill search field with the placeholder "Buscar por nombre o teléfono..."; and a white contact card with `border-radius 18px` containing a circular avatar with a purple outline, the letter "T" and a small green presence dot, the name "Tatiana Montero" in `1.15rem 700`, a pink pill badge with a heart and the text "Hermana", the phone number "317 686 6754" with a phone icon, and the line "Disponible" with a green dot; at the card's top right, a purple pencil icon button and a red trash icon button in a pale red circle. Under a hairline divider, three evenly spaced circular action tiles with labels: a purple phone "Llamar", a green WhatsApp "WhatsApp" and a purple location pin "Ubicación". Below the card, a dashed-outline card in pale purple with the text "¿Quieres agregar más?", and a circular purple floating action button with an add-person icon at the bottom right. Then the 5-tab bottom navigation with CONTACTOS active. Complies with WCAG AA.

---