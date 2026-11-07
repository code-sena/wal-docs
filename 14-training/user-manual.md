# User Manual - AlertaMujer

**Version:** 2.1 (app 1.0.0)  
**Date:** October 28, 2026  
**Platform:** Android mobile app and web portal  
**Audience:** End users

---

## Table of Contents

1. [Introduction and purpose of the system](#1-introduction-and-purpose-of-the-system)
2. [Mobile app guide](#2-mobile-app-guide)
3. [Web platform guide](#3-web-platform-guide)
4. [Frequently asked questions](#4-frequently-asked-questions)
5. [Basic troubleshooting](#5-basic-troubleshooting)
6. [Safety tips](#6-safety-tips)

---

## 1. Introduction and purpose of the system

### 1.1 What is AlertaMujer?

AlertaMujer is a personal safety app designed for women at risk. With it you can:

- Trigger an emergency alert with the ALERT button, from a contact, or by shaking your phone, even with the app minimized or the screen off.
- Notify your trusted contacts by email and by notification, with a link to follow your location in real time.
- Automatically record audio and video as evidence while the alert is active.
- Chat with your contacts while the alert lasts.
- See safe zones, risk zones and nearby help points on the map.
- Call support lines such as 123, the Línea Púrpura (Purple Line) or the mental health line.

AlertaMujer is a support tool. It does not replace official emergency services and does not guarantee that your contacts or the authorities will respond.

### 1.2 How does the system work?

The system has two parts:

1. **Mobile app (Android):** the one you carry with you. From here you trigger alerts, manage your contacts, check permissions and use the map.
2. **Web portal:** lets you review your information from a computer: alert history, your phone's location, evidence, contacts and the status of your devices. It also shows you the alerts of people who have you as a contact. It does not replace the app; it complements it.

Both use the same account, so whatever you do in one shows up in the other.

### 1.3 Security and privacy

- Your data travels encrypted (HTTPS) between the app, the web and the server.
- On your phone, your session is stored in the phone's secure storage.
- When you trigger an alert, your trusted contacts see your location, your route and the recordings of that alert while the tracking link is active. The link stops working 2 hours after the alert ends.
- While your session is open, your main phone records your day-to-day location. That location is not shared with your contacts.
- AlertaMujer administration staff can review alert information and evidence to handle cases and moderate the service.
- Information is deleted automatically after a while (see 2.13, Data retention):
  - Day-to-day locations: 7 days.
  - Routes of your alerts: 30 days.
  - Recordings: 30 days (we notify you 5 days before), except those from alerts you choose to keep from the web (up to 5 alerts).
- You can delete your account yourself from **My profile > Delete account** (see 2.14). Your personal data is anonymized.

---

## 2. Mobile app guide

### 2.1 Installing the app

#### Phone requirements

- Android 7.0 or later
- GPS turned on
- Internet connection (Wi-Fi or mobile data). Without internet the alert is not registered.
- Camera and microphone (to record evidence)

#### Installation steps

1. Download the APK file from the official link you were given.
2. Open it. If Android warns you that it can't install apps from unknown sources, tap **Settings** and allow installation for that app (your browser or your file manager).
3. Tap **Install** and wait for it to finish.
4. Open AlertaMujer.

If you already had an older version, always install the latest one. The **Download APK** button on the website (https://alerta-mujer.vercel.app) always has the most recent version. If your version is outdated, the app shows you a notice with the download link; you can tap **Later** and the alert button keeps working.

#### Permissions it will ask for

The app doesn't ask for everything at once. It asks for each permission when you are about to use the feature that needs it. You can review them all together in **Permissions and protection** (see 2.11).

| Permission | What it is used for |
|---|---|
| Location | Sending your location to your contacts when you trigger an alert. For automatic protection it asks for **Allow all the time** (see 2.17) |
| Contacts | Choosing your trusted contacts from your phone's address book |
| Notifications | Showing the countdown when you shake the phone and receiving your contacts' alerts |
| Camera | Recording video as evidence during an alert |
| Microphone | Recording audio as evidence during an alert |
| Unrestricted battery | Preventing battery saver from turning off protection while the screen is off |
| Full-screen alarm | Making the alarm for a contact's alert appear even when the phone is locked |
| Autostart | Preventing the phone from closing AlertaMujer in the background |

If you deny a permission, the app tells you which feature is limited. For example, without camera permission it cannot record video.

To call an emergency line the app doesn't need call permission: it opens your phone's dialer with the number typed in and you make the call.

### 2.2 Creating an account

1. On the home screen tap **Sign up**.
2. Fill in the **"Join us, create your account"** form:
   - **Full name**
   - **Phone**
   - **Document type** (drop-down list)
   - **Document number**
   - **Date of birth** (DD/MM/YYYY)
   - **Email**
   - **Password** and **Confirm password** (at least 8 characters, and they must match)
3. Confirm the sign-up. If everything is correct, the app shows you a confirmation message. If the email is already registered, it tells you.

By signing up or logging in you accept the Terms of Service and the Privacy Notice, which you can read in **Settings > About**.

<p align="center"><img src="img/registro.jpeg" width="260" alt="Sign-up form"></p>

### 2.3 Logging in

1. Open the app.
2. Enter your **email** and your **password**. The eye icon lets you see what you typed.
3. Tap **Log in**.

You can also log in with your Google account by tapping **Continue with Google**.

<p align="center"><img src="img/inicio-sesion.jpeg" width="260" alt="Login screen"></p>

While you use the app, the session renews itself. If you log in from a new phone, your other phones with an open session receive a notice.

#### Forgot your password?

1. On the login screen tap **Forgot your password?**
2. Enter the email you signed up with and tap **Send Code**.
3. Check your email. You will receive a security code.
4. In **Verify your identity**, enter the code and tap **Confirm Code**. The code is valid for 15 minutes and you have 5 attempts. If it expired or didn't arrive, tap **Request a new code**.
5. In **Create your new password**, enter the new password, confirm it and tap **Save password**.
6. Go back to the login screen and log in with your new password. The old one no longer works.

For security, the app shows the same message whether or not an account exists with that email.

<p align="center">
<img src="img/recuperar-1-correo.jpeg" width="200" alt="Forgot your password?">
<img src="img/recuperar-2-codigo.jpeg" width="200" alt="Verify your identity">
<img src="img/recuperar-3-nueva.jpeg" width="200" alt="Create your new password">
</p>

### 2.4 Welcome tutorial

The first time you log in, a seven-screen tutorial appears. Swipe to move forward or back, and you can skip it if you want. If you want to see it again later, go to **Settings > View Tutorials**.

| Screen | What it explains |
|---|---|
| 1. Your safety is our priority! | Support network, real-time location and immediate alert |
| 2. How the button works | Activation methods |
| 3. Alert messages | How your contacts receive the notice and the real-time location |
| 4. Your location | You choose your department and municipality |
| 5. Your support network | What your trusted contacts are for |
| 6. Video and audio security | Automatic evidence recording |
| 7. Stay informed | Alert notices even when the app is closed |

The actual notice to your contacts arrives by email and by notification in the app. The security screen says «Encrypted recording»: recordings travel protected by HTTPS, but they are not encrypted with a key of their own.

<p align="center">
<img src="img/tutorial-1.jpeg" width="190" alt="Tutorial 1">
<img src="img/tutorial-2.jpeg" width="190" alt="Tutorial 2">
<img src="img/tutorial-3.jpeg" width="190" alt="Tutorial 3">
<img src="img/tutorial-4.jpeg" width="190" alt="Tutorial 4">
</p>
<p align="center">
<img src="img/tutorial-5.jpeg" width="190" alt="Tutorial 5">
<img src="img/tutorial-6.jpeg" width="190" alt="Tutorial 6">
<img src="img/tutorial-7.jpeg" width="190" alt="Tutorial 7">
</p>

On screen 4 you choose where you live. That information is saved in your profile.

### 2.5 Home screen

When you log in you see:

- **Zone status** (top): tells you whether you are in a safe zone, whether there are incidents, whether GPS is on, and which neighborhood and municipality you are in.
- **ALERT button** (center): the big button to ask for help.
- **Last alert** (bottom): the date of your last alert and the last location sent. If you haven't triggered any, it says "No alerts".
- **Bottom bar**: Home, Map, Assistance, Contacts and History.
- **Bell** (top right): opens your notifications (see 2.18).
- **Menu** (three-line icon, top left): opens the side menu.

<p align="center"><img src="img/pantalla-principal.jpeg" width="260" alt="Home screen with the ALERT button"></p>

### 2.6 How to trigger an alert

#### 2.6.1 Ways to trigger it

Methods are managed in **Settings > Activation methods**. At the top you see how many you have active (for example "3/5 active") and each method explains how it is used.

<p align="center">
<img src="img/metodos-activacion-1.webp" width="200" alt="Activation methods, part 1">
<img src="img/metodos-activacion-2.webp" width="200" alt="Activation methods, part 2">
<img src="img/metodos-activacion-3.webp" width="200" alt="Activation methods, part 3">
</p>

1. **Main alert button:** tap the ALERT button on the home screen. The notice goes to all your trusted contacts. If you turn off this method, tapping the button does not send any alert.
2. **Alert a single contact:** go to **Contacts** and tap **Alert** on that person's card. The notice goes only to her or him.
3. **Shake phone:** shake the phone quickly and firmly, about 4 times in a row. It works even if the app is minimized or the screen is locked. You feel a vibration and have 5 seconds to cancel if it was a mistake. If you don't cancel, the alert is sent with your location without you having to unlock the phone.

While **Shake phone** is on, you will see a persistent **"Protection active"** notice in the notification bar. Don't hide it: it is what keeps detection running. To make sure it doesn't fail:

- Remove AlertaMujer from battery saver: Phone settings > Apps > AlertaMujer > Battery > Unrestricted.
- If your phone does something when shaken (turns on the flashlight, opens the camera, changes the song), turn off that gesture in the phone settings.
- Try it once in a safe place and tap **Cancel**, to learn how much force is needed.

4. **Widget:** add the AlertaMujer widget to your phone's home screen (press and hold an empty space > Widgets > AlertaMujer). Tapping it opens the same 5-second countdown, even if the app is closed.
5. **Let me know if I don't arrive:** if you are going somewhere alone, tell the app where you are going and when you will arrive; if you don't confirm that you arrived, your contacts are notified (see 2.16).

In addition, **automatic protection** goes with you, without you triggering anything, when you leave your frequent places (see 2.17).

Triggering from the lock screen or with the power button has been removed from the app.

#### 2.6.2 Countdown

With the ALERT button or with **Alert** on a contact, the **"Activating SOS Alert..."** screen appears with a 5-second countdown. With **Shake phone** the countdown appears as a notification and the phone vibrates. The screen shows what will happen when it ends:

- Send your location in real time
- Start video recording
- Start audio recording

At that moment you can:

- Tap **Activate now** so you don't have to wait.
- Do nothing: when the counter reaches zero, the alert is sent automatically. This helps if you can't touch the screen.
- Tap **Cancel Alert** if it was accidental. In that case no alert is registered. If you set up a PIN (see 2.6.7), the app asks you for it.
- Press **the volume down button four times**: this also cancels the countdown.

<p align="center"><img src="img/alerta-cuenta-regresiva.jpeg" width="260" alt="Countdown before the alert is triggered"></p>

#### 2.6.3 What happens when the alert is active

- The server registers the alert and notifies your trusted contacts by **email** and by **notification** in the app (if they have it installed). If you used **Alert** on a contact, only that person receives it.
- The screen changes to **"Active alert - Your network has been notified"**.
- You see a **Time remaining** counter.
- If no contact opens the notice within 5 minutes, it is resent once with your most recent location.
- The app sends your location in cycles while the alert is active. If there is no connection, it saves the points and uploads them when the signal returns.
- Audio and video recording starts (see 2.7).
- In **Sharing location** you see how many times your location has been opened.
- From this screen you can call the emergency line. The app opens the dialer and you make the call.
- At 25 minutes the app asks you **«Are you still in danger?»** (the time is set by the administration; by default the alert lasts 30 minutes). If you answer yes, the alert is extended. If you don't answer, the alert **does not close**: it stays active and your contacts are notified. It lasts 2 hours at most.
- If no location arrives from your phone for 5 minutes, your contacts are notified with the last known position.

<p align="center"><img src="img/alerta-activa.jpeg" width="260" alt="Active alert screen"></p>

If the server can't register the alert (for example, without internet), the app shows you the message **"The alert could not be registered on the server"**. In that case try again and, if you are in danger, call 123 directly.

#### 2.6.4 Alert chat

While the alert is active, you and your contacts can send each other messages in the alert chat. Your contacts use it from the tracking link or from the app. When the alert ends, the chat closes.

<p align="center"><img src="img/alerta-chat.jpeg" width="260" alt="Alert chat"></p>

#### 2.6.5 What your contacts receive and see

Your contacts don't need to have the app to find out:

- **By email:** they receive a notice with a tracking link and, as a backup, a Google Maps link with your location. For this, the contact must have an email registered (see 2.8).
- **By notification:** if the contact has AlertaMujer installed and is logged in, they receive a high-priority notification that **sounds like an alarm** until they silence it (with a volume button, by swiping the notification away, or with the **Silence alarm** button), and it opens the tracking directly.

In your history you can see, for each contact, whether the notice was sent, delivered, **«Already seen»** or **«Not seen in time»**.

In the tracking view (web page or **"Emergency alert"** screen in the app) they see:

- A map with your current location and the **Open map** button
- Your GPS coordinates and the date and time of the last update
- The history of locations sent during the alert
- The alert's recordings and the chat

<p align="center"><img src="img/alerta-vista-contacto.jpeg" width="260" alt="Tracking view seen by the contact"></p>

The link is unique for each alert and stops working 2 hours after it ends.

#### 2.6.6 How to cancel an active alert

1. On the active alert screen tap **I'm okay**.
2. Confirm with **Yes, cancel**. If you set up a PIN, the app asks you for it.
3. Your contacts receive a notice that the alert ended, and your location stops being shared.

<p align="center"><img src="img/alerta-cancelar-pin.jpeg" width="260" alt="PIN requested to cancel the alert"></p>

#### 2.6.7 Alert security: PIN and emergency PIN

In **Settings > Alert security** you can set a **PIN** so nobody else can cancel your alert. With the PIN on, the app asks for it to cancel the countdown, to tap **I'm okay** and to stop automatic protection.

You can also set an **emergency PIN** (optional and different from the normal PIN). If someone forces you to cancel, enter the emergency PIN: on your phone the alert will look canceled, but it continues silently and your contacts receive the notice.

<p align="center"><img src="img/seguridad-pin.jpeg" width="260" alt="Setting up the PIN and emergency PIN"></p>

### 2.7 Evidence recording (audio and video)

When an alert is triggered, the app starts recording on its own. It doesn't ask you to confirm anything at that moment so you don't lose time.

- It records **audio** with the microphone, even with the screen off.
- It records **video** with the camera.
- Recordings are made in segments of about 45 seconds. Each segment is uploaded to the cloud as soon as it is ready; if there is no connection, it stays in a queue and is uploaded when the signal returns.
- Each file gets a digital fingerprint (SHA-256) that lets you verify it hasn't been modified.

If you don't want the app to use the camera or microphone, turn off these options in **Settings > Evidence and privacy**:

- *Automatically record video when an emergency is activated*
- *Automatically record audio when an emergency is activated*

<p align="center"><img src="img/configuracion.jpeg" width="260" alt="Evidence and privacy options"></p>

Recordings are deleted after 30 days; we notify you 5 days before. To view, download or keep them, go to the web portal, **Evidence** section (see 3.9).

### 2.8 Trusted contacts

These are the people who receive the notice when you trigger an alert. Open it from **Contacts** in the bottom bar or from the side menu.

#### Viewing your contacts

In **My Trusted Contacts** you see how many contacts you have and a search box by name or phone. Each card shows the name, the relationship (for example "Family"), the phone and whether they are available. Below each contact you have three buttons:

- **Call**
- **WhatsApp**
- **Alert:** triggers an alert that goes only to that contact. It goes through the same 5-second countdown.

<p align="center"><img src="img/contactos.jpeg" width="260" alt="Trusted contacts list"></p>

#### Adding a contact

1. Tap **+ Add** (or the round button with the person icon).
2. Fill in the contact details: name, phone number, relationship (mom, partner, friend...) and email.
3. If you want, import it from your phone's address book. The first time, the app asks for permission to see your contacts.
4. Tap **Save**.

The email is important: it is how a contact who doesn't have the app receives the notice with the tracking link.

The maximum number of contacts is set by the administration (5 by default). If you have already reached the limit, the system won't let you add another one until you delete one. The number must be in a valid format; if it is from another country, include the country code (for example, +57 for Colombia).

<p align="center"><img src="img/contacto-agregar.jpeg" width="260" alt="Adding a trusted contact"></p>

#### Editing or deleting a contact

- **Edit:** tap the pencil on the card, change what you need and save.
- **Delete:** tap the red trash can and confirm.

<p align="center"><img src="img/contacto-editar-eliminar.jpeg" width="260" alt="Updating or deleting a contact"></p>

### 2.9 Map

Tap **Map** in the bottom bar.

#### Your location

At the top you see your current address. Below there is an address search box and a small map with your position. Tap **Open full map** to see it in full screen; there you can search for addresses, center the map on yourself and go back with **Close map**.

<p align="center">
<img src="img/mapa.jpeg" width="230" alt="Map screen">
<img src="img/mapa-completo.jpeg" width="230" alt="Full-screen map">
</p>

Further down you will find:

- **Show routes:** shows on the map the routes you have saved (up to 4).
- **Important only:** shows only the routes marked with a star (up to 2).
- **Share, Update, Navigate and Save:** to share your location, refresh it, open navigation or save the place where you are.
- **Latitude and longitude** with the time of the last update.

<p align="center">
<img src="img/mapa-recorridos.jpeg" width="230" alt="Route options and quick actions">
<img src="img/mapa-acciones.jpeg" width="230" alt="Additional map actions">
</p>

Under **Additional actions** you have:

- **Saved locations**
- **Auxiliary zones**
- **Save Route:** creates a route from point A to point B.
- **Route history:** places you have passed through.

#### Saving and classifying a zone

This is how you mark a place as safe or dangerous. It also helps other users.

1. Tap **Save** on the map.
2. In **Classify zone**, place the point: search for the address or tap **Use my current location**.
3. Give it a name or use the **Home**, **Work** or **Other** shortcuts.
4. Fill in the zone information: address, city, neighborhood or commune, a description (up to 250 characters) and, if you want, personal notes (up to 200).
5. Choose the **Safety level**:
   - **Very safe**
   - **Moderate** (medium risk)
   - **Very unsafe** (very high risk)
6. Tap **Save Location**.

The administration team reviews reported zones before they count on everyone's map.

<p align="center">
<img src="img/clasificar-zona-1.jpeg" width="230" alt="Classify zone, part 1">
<img src="img/clasificar-zona-2.jpeg" width="230" alt="Classify zone, part 2">
</p>

#### Saved locations

Here you see all the places you saved. You can search them by name, address or city and filter them by **All**, **Safe**, **Moderate** or **Unsafe**. To delete one, tap the red trash can.

<p align="center"><img src="img/ubicaciones-guardadas.jpeg" width="260" alt="Saved locations"></p>

#### Route history

Shows the routes you have saved, with the date, the distance and the start and end points. The star marks a route as important and the trash can deletes it. You can have up to 4 routes and 2 important ones; to save a new one when you already have 4, delete one.

<p align="center"><img src="img/historial-recorridos.jpeg" width="260" alt="Route history"></p>

#### Auxiliary zones

Shows zones and help points near you. The map legend indicates:

| Color | Meaning |
|---|---|
| Green | Safe zone |
| Red | Risk zone |
| Blue | Assistance point |
| Purple | Police |

In **Nearby zones** you can filter by All, Safe, Risk or Assistance.

<p align="center"><img src="img/zonas-auxiliares.jpeg" width="260" alt="Auxiliary zones"></p>

### 2.10 Assistance channels

Tap **Assistance** in the bottom bar. There you will find the lines you can call, each with its **Call now** button:

- **Immediate emergency - 123:** help for any emergency.
- **Línea Púrpura (Purple Line) - 018000112137:** guidance and support for women experiencing violence.
- **Mental Health Line - 106:** psychological support and emotional containment.

Scroll down to see the rest of the directory. Tapping **Call now** opens your phone's dialer with the number typed in; you make the call.

<p align="center"><img src="img/canales-asistencia.jpeg" width="260" alt="Assistance channels"></p>

### 2.11 Permissions and protection

Open it from the side menu, **Permissions and protection** option. This screen checks everything the app needs to protect you and tells you how many items are ready (for example "8/9 ready").

- Each item appears as **On** or **Missing**. Missing items have an **Enable** button. If Android no longer lets the app ask, the button opens the AlertaMujer settings on your phone.
- It checks: Shake the phone, Location, Notifications, Unrestricted battery, Full-screen alarm, Autostart, Microphone, Camera and Phone contacts.
- If protection stopped in the last few days (for example, because the app closed due to an error), a notice at the top shows how many times it happened.

<p align="center">
<img src="img/permisos-proteccion-1.webp" width="200" alt="Permissions and protection, part 1">
<img src="img/permisos-proteccion-2.webp" width="200" alt="Permissions and protection, part 2">
<img src="img/permisos-proteccion-3.webp" width="200" alt="Permissions and protection, part 3">
</p>

### 2.12 Alert history

Tap **History** in the bottom bar. At the top you see a summary:

- **Total** alerts
- **Sent**
- **Canceled**
- **In progress**

You can search for an alert by date, place or status. When you open an alert you see its details, including the status of the notice to each contact. If you haven't triggered any yet, you will see "No alerts found".

<p align="center"><img src="img/historial-alertas.jpeg" width="260" alt="Alert history"></p>

### 2.13 Side menu and settings

Tap the three-line icon at the top left. At the top of the menu you see your photo, your name and your email. Below are **Home**, **Contacts**, **History**, **Permissions and protection** and **Settings**. Further down are **Share Link** (to invite other women) and **Log out**.

<p align="center"><img src="img/menu-lateral.jpeg" width="260" alt="Side menu"></p>

In **Settings** you will find:

| Option | What it does |
|---|---|
| Light / dark mode | Switches between light and dark theme |
| Default themes | Changes the app's colors |
| Language | Español, English, Português or Français |
| Activation methods | Choose how an alert can be triggered (see 2.6.1) |
| Notifications | Turns notices, vibration and quiet hours on or off |
| Evidence and privacy | Automatic video and audio recording |
| App privacy | Privacy options and retention times for your data |
| About | Version, terms and privacy policy |
| View Tutorials | Shows the tutorial again |

#### Appearance

You can change the app theme with the light or dark mode switch and choose one of the default colors. There are six themes in total.

<p align="center"><img src="img/pantalla-principal-oscura.jpeg" width="260" alt="Home screen in dark theme"></p>

#### Language

Tap **Language**, choose the one you want and tap **Close**. The whole app changes language, and it stays that way the next time you open it.

<p align="center"><img src="img/idioma.jpeg" width="260" alt="Language selection"></p>

#### Notifications

- **Enable notifications:** if you turn it off, you won't get any notices from the app.
- **Vibration:** the phone vibrates when a notification arrives.
- **Quiet hours (do not disturb):** you choose a time range, for example from 22:00 to 07:00, during which you don't receive notices.

The screenshot shows this screen with the app in Portuguese.

<p align="center"><img src="img/notificaciones.jpeg" width="260" alt="Notification settings"></p>

#### Data retention

Explains how long the app keeps your information on the server:

- **Location history:** the routes of your alerts are kept for 30 days and day-to-day locations for 7 days. After that they are deleted automatically.
- **Evidence (videos and audio):** kept for 30 days, with a notice 5 days before. If you need them as proof, for example for a police report, you can keep up to 5 alerts from the web, in **Evidence**.
- **Profile data:** used only for the alerts to work and not sold to third parties.

<p align="center"><img src="img/retencion-datos.webp" width="260" alt="Data retention"></p>

#### About

Shows the app version (1.0.0) and lets you read two documents:

- **Terms and conditions:** what the service is, how you must use it and what happens if it is misused. For example, generating false alerts or using the app to harass someone can lead to account suspension. It also makes clear that AlertaMujer is a support tool and does not replace official emergency services.
- **Privacy policy:** what data the app collects (location, contacts, camera and microphone), what it uses it for, who it shares it with, where it is stored and what rights you have over it.

<p align="center">
<img src="img/acerca-de.jpeg" width="200" alt="About screen">
<img src="img/terminos.jpeg" width="200" alt="Terms and conditions">
<img src="img/politica-privacidad.jpeg" width="200" alt="Privacy policy">
</p>

### 2.14 My profile

Tap your photo or your name at the top of the side menu. In **My profile** you can:

- Change your photo (tap the camera icon)
- Update your full name, email, phone and date of birth
- See your registered location: department and municipality

Your name, phone and photo can only be edited from the app. On the web you can change your password, but not edit your profile.

At the bottom of **My profile** is **Delete account**. Tap **Delete my account** and confirm: your account is closed and your personal data is anonymized. This cannot be undone.

<p align="center"><img src="img/perfil.jpeg" width="260" alt="My profile"></p>

### 2.15 Logging out

Open the side menu and tap **Log out**. Keep in mind that, without a session, shake-the-phone protection and location tracking stop.

### 2.16 Let me know if I don't arrive

Use it when you are going somewhere alone. Open it from the side menu (**Let me know if I don't arrive**) or from the **Going somewhere?** card on the home screen.

1. Type **Where are you going?** or choose the point on the map.
2. Choose **When will you arrive?** (between 5 minutes and 4 hours) and tap **Start trip**. Do it while you have a connection.
3. While you are on your way, your phone sends a signal with your location every 2 minutes, even if the app is closed.
4. Five minutes before the time, we ask you whether you have arrived. When you are less than 100 m from the destination, we also ask you. If you confirm, the trip ends.

If you don't confirm 2 minutes after the arrival time, or if your phone stops sending a signal, the server triggers an alert for you with your last location and notifies your contacts. It doesn't depend on your phone having data at that moment. You can only have one active trip at a time.

<p align="center"><img src="img/avisame-si-no-llego.jpeg" width="260" alt="Let me know if I don't arrive"></p>

### 2.17 Automatic protection

When you leave one of your frequent places (home, work, university), the app goes with you without you having to start anything.

**To turn it on:** go to **Settings > Alert security > Automatic protection**. You need at least one place saved on the map (see 2.9) and the **Allow all the time** location permission; without that permission it doesn't work with the app closed.

**How it works:**

- When you leave a frequent place, the protection notification appears and your phone sends its location every 2 minutes.
- If you stay still for 15 minutes in a place that isn't frequent, we silently ask you **«Did you arrive safely?»**. If you don't answer, we ask again at 25 minutes and, if you still don't answer, at 35 minutes we notify your contacts with your last location.
- If you stop in a risk zone, we ask you **«Is everything okay?»**. If you don't answer within 5 minutes, we notify your contacts.
- If your phone stops sending a signal for 20 minutes, the server notifies your contacts.
- When you reach another frequent place, protection ends on its own.

**Responding or stopping it:** to tap **Yes, I arrived safely**, **I'm okay** or **Stop** in the notification, the phone asks you to unlock it (on Android 12 or later). If you set up a PIN (see 2.6.7), **Stop** opens the app and asks you for it; with the emergency PIN the protection appears to stop, but your contacts are notified silently.

<p align="center"><img src="img/proteccion-automatica.jpeg" width="260" alt="Automatic protection in Alert security"></p>

### 2.18 Notifications (bell)

Tap the **bell** at the top to see everything AlertaMujer has notified you about: alerts from your contacts, notices about your alerts and news. Tap a notification to open it. **Clear all** empties the list without affecting your alerts.

<p align="center"><img src="img/notificaciones-campana.jpeg" width="260" alt="Bell notifications"></p>

---

## 3. Web platform guide

The web portal is for reviewing your information from a computer. It works best on desktop screens and in recent versions of Chrome, Edge or Firefox. Alerts are triggered from the mobile app, not from the web.

### 3.1 Landing page

When you go to the portal's address you see AlertaMujer's public page. At the top are the menu (**Home**, **Features**, **How it works**, **Testimonials**, **FAQ** and **Contact**), the language selector, the dark mode button and the **Log in** and **Sign up** buttons.

<p align="center"><img src="img/web/usuaria/landing.png" width="700" alt="Public landing page of the web portal"></p>

### 3.2 Sign-up

1. Tap **Sign up**.
2. Fill in the **"Join us, create your account"** form:
   - **Full name**
   - **Phone** and **date of birth** (DD/MM/YYYY)
   - **Document type** and **document number**
   - **Email**
   - **Password** and **Confirm password** (at least 8 characters)
3. Open **Read terms** and **Read policy**, and check both acceptance boxes.
4. Tap **Continue**.

If you already have an account in the app, don't sign up again: use the same email and password.

<p align="center"><img src="img/web/usuaria/registro.png" width="700" alt="Sign-up form on the web"></p>

### 3.3 Logging in

1. Tap **Log in**.
2. Enter your **email** and your **password** (the same as in the app).
3. If you are on your personal computer, you can check **Remember me**.
4. Tap **SIGN IN**.

You can also log in with **Continue with Google**. If you don't have an account, tap **Create an account**.

<p align="center"><img src="img/web/usuaria/login.png" width="700" alt="Login on the web"></p>

The web session lasts 24 hours and also closes if you leave the portal unused for a while. When that happens, log in again.

### 3.4 Recovering your password

1. On the login screen tap **Forgot your password?**
2. Enter your email and tap **SEND INSTRUCTIONS**.
3. In **Verify your code**, enter the 6-digit code you received by email. The screen shows how much time is left; if it expires, tap **Resend code**.
4. Tap **VERIFY CODE** and create your new password.

The code is valid for 15 minutes and you have 5 attempts. Recovery is done by email.

<p align="center">
<img src="img/web/usuaria/recuperar-contrasena.png" width="420" alt="Forgot your password? on the web">
<img src="img/web/usuaria/verificar-codigo.png" width="420" alt="Verify your code">
</p>

### 3.5 Main dashboard

After logging in you land on **Home**. The screen has three parts:

- **Side menu (left):** Home, My emergencies, My contacts' alerts, Location, Evidence, Emergency contacts, Assistance, Device Status and Settings. At the bottom are the **Open mobile app** button, your name, your email and the log out button.
- **Top bar:** your greeting, the notification bell, the dark mode button and the **Language** menu.
- **Content:** changes depending on the menu option.

In **Home** you will find:

- **Your safety status:** how ready your account is (for example "Needs attention - 75% configured"). It checks four items: how many emergency contacts you have, how many receive the link (have an email), how many phones you have linked and whether the phone's notifications are on. The **View contacts** button takes you to your contacts.
- **My device:** your phone's model, its alerts and its last connection. Tap **View device details** to go to section 3.11.
- **My phone's last location:** a map with the last location your phone sent. **Update location** asks the phone for its current location and **View full map** opens the Location section. Below the map are the **Map guide** (my phone, risk zone, safe zone, police station, assistance center and help point) and the details of that location: address, date and time, coordinates and accuracy.
- **Last emergency:** date and time, location, how it was triggered, how many contacts were notified and its status. The **View my emergencies** button opens the history.

<p align="center">
<img src="img/web/usuaria/inicio-1.png" width="700" alt="Web portal home, top section">
</p>
<p align="center">
<img src="img/web/usuaria/inicio-2.png" width="700" alt="Web portal home, bottom section">
</p>

The location you see is the last one your phone recorded. If the phone is off or without internet, the web can't know where it is now.

You only see your own account's information.

### 3.6 My emergencies

Here are all the alerts you have triggered from the app.

- At the top there is a search box by date, activation method or status, and filters by period (**All dates**) and by activation method.
- The cards show the **Total alerts**, the **Resolved** ones and the **Canceled** ones.
- The table shows, for each alert, the number, the date and time, the activation method (for example **Panic button**), the location and the status (**Canceled**, **Resolved**...).
- Tap the eye icon to see an alert's details.

<p align="center"><img src="img/web/usuaria/mis-emergencias.png" width="700" alt="My emergencies"></p>

### 3.7 My contacts' alerts

Shows the alerts from the last 30 days of the people who registered you as their emergency contact. If nobody has triggered an alert, you will see "No contact has triggered an alert in the last 30 days".

For the browser to notify you when an alert arrives, allow the site's notifications. If they are blocked, the screen tells you: you can enable them from the padlock next to the browser's address bar.

<p align="center"><img src="img/web/usuaria/alertas-contactos.png" width="700" alt="My contacts' alerts"></p>

### 3.8 Location

It has four tabs at the top:

- **My location:** a map with the computer's live location, the neighborhood, the address and the accuracy. Tap **Center on me** to go back to your point. With the **History** button you see the locations your phone has recorded.
- **Saved locations:** the places you saved and classified from the app.
- **Auxiliary zones:** safe zones, risk zones and help points.
- **Save route:** your saved routes.

The left panel also has:

- **My phone:** tap **View phone location**. The web asks the phone for its current location; if it doesn't respond, you see the last one it sent.
- **Map layers:** switches to show or hide your routes, only the important ones, your saved locations and the auxiliary zones.

Below the map is the legend: you, locations, safe zone, risk, police and assistance.

<p align="center"><img src="img/web/usuaria/ubicacion.png" width="700" alt="Location in the web portal"></p>

### 3.9 Evidence

Here are the photos, videos and audio from your alerts. They can be seen by you and by the contacts who received each alert while its tracking link is active.

**Choosing the alert**

- On the left is the **Your alerts** list, with the date, the place, the status and how many files each one has. Check **Only with evidence** to hide alerts without files and use the search box by date, neighborhood or status.
- Below the list you can filter by **All**, **Videos** or **Audio**.
- The app records in segments. In **Full recording** are the **Watch full video** and **Listen to full audio** buttons, which play all the segments in a row without opening them one by one.

**Viewing a file**

When you choose a file, it plays on the right and its details are shown: type, size, when it was saved, when it will be deleted, which alert it belongs to, when the alert started and where you were (with the **View on map** link). With the pencil next to the name you can rename the file.

The **SHA-256 fingerprint** also appears. If someone modifies the file, that fingerprint stops matching, so it serves as proof that the recording was not altered.

**Keeping an alert's evidence**

Evidence is deleted after 30 days. If you need it, for example for a police report:

1. Choose the alert in the list.
2. Tap **Keep** in the **Deleted after 30 days** box.
3. You can keep up to 5 alerts. The box shows how many you have used (for example "0 of 5 used").

**Downloading evidence**

1. Select the evidence.
2. Tap **Download**.
3. The file is saved on your computer.

**Deleting evidence**

1. Select the evidence.
2. Tap **Delete**.
3. Confirm.

**Uploading evidence**

1. Tap **Upload evidence** (top right).
2. Choose the file on your computer (50 MB maximum).
3. Choose which alert it belongs to.

<p align="center"><img src="img/web/usuaria/evidencias.png" width="700" alt="Evidence"></p>

### 3.10 Emergency contacts

Here you manage the people who receive your alerts. Changes you make also appear in the app.

- The table shows each contact's name, phone, email, relationship and actions.
- To add one, tap **+ Add contact** and fill in the details.
- To edit, tap the pencil. The three-dot menu has the other options, such as delete.

All your contacts receive a notice when you trigger an alert. Add their email so they receive the link with your location.

<p align="center"><img src="img/web/usuaria/contactos.png" width="700" alt="Emergency contacts"></p>

### 3.11 Assistance

Help lines and support centers.

- At the top is the **Are you in danger right now?** notice with the **123** button.
- The **Help lines**, **Support centers** and **My calls** tabs let you see the directory and the calls you have made from the app.
- Each line has a **Copy number** button. The call is made from your phone.
- At the bottom of the screen there is a reminder that in Colombia you can also call **Line 155** (guidance for women who are victims of violence) from any carrier.

<p align="center"><img src="img/web/usuaria/asistencia.png" width="700" alt="Assistance"></p>

### 3.12 Device status

A diagnostic to check that everything is ready in case you need to trigger an alert. The button at the top right refreshes it.

- **Your phone's permissions:** the status of the app's permissions. If it doesn't appear, open the updated app on your phone.
- **Services Status:** time left in the web session, linked phone, whether you receive alerts on your phone, last recorded location, connectivity and connection status. Each item shows as active or inactive.
- **My devices:** the phones linked to your account, with their system, app version and last connection.

<p align="center"><img src="img/web/usuaria/estado-dispositivo.png" width="700" alt="Device status"></p>

### 3.13 Settings

In **Settings** (the **Preferences** screen) you will find:

| Section | What you can do |
|---|---|
| Account | Change your password and see your email and phone. Name, phone and photo are edited from the app, in Profile |
| Appearance | Light or dark theme and main color |
| Notifications | Turn on or off emergency alerts, app updates, safety reminders, location notifications, and sounds and vibration |
| Your data | Explains who sees your location and your recordings and how long they are kept |
| Emergency | Shortcuts to emergency contacts, my alerts, zones and help points, and help lines |
| Support and help | Terms and conditions, privacy policy and version (1.0.0) |

<p align="center"><img src="img/web/usuaria/configuracion.png" width="700" alt="Settings"></p>

#### Notifications and language

- The **bell** in the top bar shows your notifications. If there are none, it says "You have no notifications".
- The **Language** button opens a menu to choose Español, English, Français or Português. For now the web saves your choice as a preference, but the portal's text stays in Spanish.

<p align="center"><img src="img/web/usuaria/idioma.png" width="260" alt="Language menu"></p>

### 3.14 Logging out

Tap the button next to your name, at the bottom of the side menu, or open the **Language** menu and tap **Log Out**. If you are on a computer that other people use, always log out when you finish.

---

## 4. Frequently asked questions

### 4.1 General

**Can I use the app without internet?**  
Not to send alerts. Registering the alert and notifying your contacts requires internet. If the connection drops with the alert already active, the app saves your location and the recordings and uploads them when the signal returns.

**Do my contacts need to have the app?**  
No. They receive an email with a link to see your location. If they have the app, they also receive a notification.

**What happens if I change my number?**  
Update it in **My profile** in the app.

**What languages is the app available in?**  
Spanish, English, Portuguese and French.

### 4.2 Alerts

**What happens if I trigger the alert by accident?**  
If the countdown is still running, tap **Cancel Alert**. If it was already sent, tap **I'm okay** and then **Yes, cancel**. Your contacts receive a notice that the alert ended.

**How long does an alert last?**  
Until you tap **I'm okay**. At 25 minutes we ask you whether you are still in danger; if you don't answer, the alert stays active and your contacts are notified. It lasts 2 hours at most.

**Can I trigger an alert without GPS?**  
Yes, but without a location. If there is no GPS signal, the last known location is sent. It's best to always keep GPS on.

**Do my contacts see my location all the time?**  
No. Only during an alert and up to 2 hours after it ends, while the link is still active.

**Does the app work if it is minimized or with the screen off?**  
Yes, with the **Shake phone** method on. Some phones close apps to save battery; check **Permissions and protection** and item 5.2.

**Can I notify just one person instead of all my contacts?**  
Yes. In **Contacts**, tap **Alert** on the card of the person you want to notify.

**Can I trigger the alert from the widget or the lock screen?**  
From the widget, yes (see 2.6.1). Triggering from the lock screen has been removed from the app.

**What happens if someone forces me to cancel the alert?**  
Use your emergency PIN (see 2.6.7): the alert looks canceled, but it continues silently and your contacts receive the notice.

### 4.3 Contacts

**How many contacts can I add?**  
The limit is set by the administration. By default it is 5.

**What do my contacts see when I trigger an alert?**  
Your name, that you triggered an emergency alert, your location on the map, your coordinates, the location history of that alert, the recordings and the chat.

**Can I add contacts from another country?**  
Yes. Enter the number with the country code.

### 4.4 Evidence

**What does the app record?**  
Audio and video from the moment the alert is triggered, in segments of about 45 seconds.

**Can I make it not record?**  
Yes. Turn off video recording, audio recording or both in **Settings > Evidence and privacy**.

**Where is my evidence stored?**  
It is uploaded to the cloud and you see it in the web portal, in **Evidence**. You can also download it from there.

**How long is it kept?**  
30 days; we notify you 5 days before. From the web you can keep the evidence of up to 5 alerts.

**Can I delete evidence?**  
Yes, from the web portal in **Evidence > Delete**.

### 4.5 Privacy

**Who can see my location?**  
Your trusted contacts during an alert (and up to 2 hours after). Your day-to-day location is not shared with your contacts. Administration staff can check alert information to handle them.

**Can I delete my account?**  
Yes, from **My profile > Delete account** in the app. Your personal data is anonymized.

**Is my data protected?**  
It travels encrypted, your session on the phone is stored in secure storage, and one user cannot see another user's information.

---

## 5. Basic troubleshooting

### 5.1 GPS

**GPS doesn't work**

1. Check that location is turned on on your phone.
2. Check that AlertaMujer has location permission (Phone settings > Apps > AlertaMujer > Permissions).
3. Close and reopen the app.
4. If it still doesn't work, restart your phone.

**The location isn't accurate**

1. Go to an open area, away from buildings.
2. Wait a few seconds for the GPS to adjust.
3. Turn on high-accuracy mode in your phone's location settings.

### 5.2 Notifications and protection

**I'm not getting notifications**

1. Check that they are turned on in **Settings > Notifications** inside the app.
2. Check that you are not within your quiet hours.
3. Check the notification permissions in your phone settings.
4. Check your internet connection.

**Shake the phone doesn't work or stops**

1. Go to **Permissions and protection** and enable the items that say **Missing**.
2. Go to Phone settings > Apps > AlertaMujer > Battery and choose **Unrestricted**.
3. Turn on AlertaMujer's **Autostart** if your phone has that option.
4. Don't hide the **"Protection active"** notification.
5. Check that your session is still open.

**Automatic protection doesn't go with me when I leave home**

1. Check that it is on in **Settings > Alert security**.
2. Give location the **Allow all the time** permission.
3. Check that you have at least one place saved on the map.
4. Remove AlertaMujer from battery saver.

### 5.3 Logging in

**I can't log in**

1. Check that your email and password are typed correctly.
2. If you forgot your password, use **Forgot your password?**
3. Check your internet connection.
4. If you made many attempts in a row, wait a minute: for security, the system briefly blocks repeated attempts.

**My web session closed on its own**

That's normal. For security, the web session expires after 24 hours or after a period of inactivity. Log in again.

**My account shows as blocked**

Only the administration team can block or unblock accounts. Write to the support email.

### 5.4 The app

**It closes on its own or freezes**

1. Close the app from recent apps and open it again.
2. Check that you have free space on your phone.
3. Restart your phone.
4. If it keeps happening, uninstall and reinstall the latest version.

**The alert takes a long time to register or says "The alert could not be registered on the server"**

1. Check your internet connection.
2. Trigger the alert again.
3. If you are in danger, don't wait: call 123.

**I can't add contacts**

1. Check that you haven't reached the contact limit. If you have, delete one so you can add another.
2. Check your internet connection.
3. Fill in all the required fields.
4. Check that the number is in a valid format.
5. If you want to import from your address book, check that the app has contacts permission.

**Evidence isn't being recorded**

1. Check that automatic recording is turned on in **Settings > Evidence and privacy**.
2. Check that the app has camera and microphone permission (in **Permissions and protection**).
3. Check that you have free space on your phone.

### 5.5 Web portal

**It doesn't load**

1. Check that the address is typed correctly.
2. Try another browser (Chrome, Edge or Firefox).
3. Clear the cache and cookies.
4. Check your internet connection.
5. If it's the first time you've opened it in a while, wait a minute or two and reload: the server may be starting up.

**The map doesn't show**

1. Use a recent version of Chrome, Edge or Firefox.
2. Check that JavaScript is enabled.
3. Try another browser.

**I'm not receiving the password recovery code**

1. Check that the email is typed correctly and that it is the same one you signed up with.
2. Look in your spam folder.
3. If the code expired, request a new one.

### 5.6 Help lines

If you are in danger, don't wait to solve a technical problem. Call directly:

- **Emergencies:** 123
- **Línea Púrpura (Purple Line):** 018000112137
- **Line 155:** guidance for women who are victims of violence
- **Mental Health Line:** 106

---

## 6. Safety tips

### 6.1 Using the app

- Don't share your password with anyone.
- Keep the app updated.
- Check **Permissions and protection** from time to time.
- If you get a notice that someone logged into your account from a new phone and it wasn't you, change your password.
- If you use a shared computer, log out.

### 6.2 Contacts

- Choose people who can respond quickly.
- Register each contact's email.
- Let them know they may receive emergency messages from you and what they look like.
- If someone changes their number or email, update it.

### 6.3 Alert button

- Do a test with your contacts so they know what they will receive.
- Use it only in real emergencies.
- If you trigger it by accident, cancel it right away.
- Keep GPS on.

### 6.4 Zones

- Check the map before going out and avoid risk zones when you can.
- Mark the zones you know. What you register helps other women.
- Save your frequent places (home, work) so you have them at hand.

---

**End of User Manual**
