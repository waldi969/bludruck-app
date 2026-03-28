# Firebase Security & Setup Guide

Dieses Dokument hilft Ihnen, Ihre Firebase-Datenbank abzusichern.

## 1. Firebase Authentication aktivieren (NEU)

Damit nur Sie Zugriff auf Ihre Daten haben, müssen wir den Google-Login aktivieren:

1. Gehen Sie in die [Firebase Console](https://console.firebase.google.com/).
2. Klicken Sie links auf **Authentication**.
3. Klicken Sie auf den Tab **Sign-in method**.
4. Klicken Sie auf **Add new provider** und wählen Sie **Google**.
5. Aktivieren Sie den Schalter oben rechts, geben Sie eine Support-E-Mail an und klicken Sie auf **Save**.

## 2. Firestore Security Rules verschärfen

Ersetzen Sie Ihre alten Regeln durch diese, damit jeder Nutzer nur seine **eigenen** Daten sehen kann:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /messungen/{document} {
      // Erlaubt das Lesen/Schreiben nur, wenn der Nutzer angemeldet ist
      // und die userId im Dokument mit der ID des angemeldeten Nutzers übereinstimmt.
      allow read, write: if request.auth != null && request.auth.uid == (request.resource.data.userId || resource.data.userId);
    }
  }
}
```

## 3. Datenbank-Index erstellen

Da wir nun nach `userId` filtern UND nach `zeitpunkt` sortieren, benötigt Firebase einen "zusammengesetzten Index" (Composite Index).

1. Wenn in der App die Meldung "Wahrscheinlich fehlt ein Datenbank-Index" erscheint, öffnen Sie die Browser-Konsole (F12).
2. Dort finden Sie einen Link, der Sie direkt zur Firebase Console führt, um den Index mit einem Klick zu erstellen.

## 4. API-Schlüssel auf Ihre Domain einschränken

In einer reinen Browser-App (HTML/JS) ist der Schlüssel technisch immer sichtbar.

**Lösung (Einschränkung auf GitHub Pages):**
1. Gehen Sie zur [Google Cloud Console - API & Services](https://console.cloud.google.com/apis/credentials).
2. Klicken Sie auf Ihren API-Schlüssel (AIzaSy...).
3. Unter **Set an application restriction** wählen Sie **Websites**.
4. Fügen Sie Ihre GitHub-Pages URL hinzu: `https://waldi969.github.io/*`.
5. Speichern Sie. (Es kann bis zu 5 Min. dauern, bis die Änderung greift).
