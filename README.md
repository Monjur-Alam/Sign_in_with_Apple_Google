# Sign in with Apple & Google - UIKit (iOS)

This project demonstrates how to implement **Sign in with Apple** and **Sign in with Google** using **UIKit** and fetch user information (Full Name, Email) on first login.

---

## ✅ Features
- Apple and Google Sign-in buttons (UIKit)
- Full name and email retrieval (on first login)
- User info storage using `UserDefaults`
- User identification via `userIdentifier` (Apple) or Google `userID`

---

## 🧱 Requirements
- iOS 13+
- Xcode 11+
- Apple Developer Account (with Sign in with Apple enabled)
- Google Developer Console project (with OAuth client ID)

---

## 🔧 Setup Instructions

### 1. Apple Sign In Setup
- Go to [Apple Developer Portal](https://developer.apple.com/account/)
- Enable **Sign in with Apple** for your App ID
- In Xcode, go to **Signing & Capabilities** > Add **"Sign in with Apple"**

### 2. Google Sign In Setup
- Go to [Google Cloud Console](https://console.cloud.google.com/)
- Create a new project or use an existing one
- Go to **APIs & Services > Credentials**
- Create an **OAuth 2.0 Client ID** for iOS
- Add your app’s **Bundle ID**
- Download the `GoogleService-Info.plist` and add it to your Xcode project

Install Google Sign-In SDK via Swift Package Manager:
```
https://github.com/google/GoogleSignIn-iOS
```

In `Info.plist`, add:
```xml
<key>CFBundleURLTypes</key>
<array>
  <dict>
    <key>CFBundleURLSchemes</key>
    <array>
      <string>com.googleusercontent.apps.YOUR_CLIENT_ID</string>
    </array>
  </dict>
</array>
```

---

## 👤 Apple Sign-In - UIKit

### 1. Import Authentication Services
```swift
import AuthenticationServices
```

### 2. Add Button and Handle Request
```swift
let appleButton = ASAuthorizationAppleIDButton()
appleButton.addTarget(self, action: #selector(handleAppleSignIn), for: .touchUpInside)
view.addSubview(appleButton)
```

### 3. Handle Delegate
```swift
extension YourViewController: ASAuthorizationControllerDelegate, ASAuthorizationControllerPresentationContextProviding {

    func presentationAnchor(for controller: ASAuthorizationController) -> ASPresentationAnchor {
        return self.view.window!
    }

    func authorizationController(controller: ASAuthorizationController, didCompleteWithAuthorization authorization: ASAuthorization) {
        if let credential = authorization.credential as? ASAuthorizationAppleIDCredential {

            let userID = credential.user
            let fullName = credential.fullName
            let email = credential.email

            if let given = fullName?.givenName, let family = fullName?.familyName, let mail = email {
                let name = "\(given) \(family)"
                print("✅ First-time user: \(name), \(mail)")

                UserDefaults.standard.set(name, forKey: "userFullName")
                UserDefaults.standard.set(mail, forKey: "userEmail")
            } else {
                let savedName = UserDefaults.standard.string(forKey: "userFullName") ?? "Unknown"
                let savedEmail = UserDefaults.standard.string(forKey: "userEmail") ?? "Unknown"
                print("✅ Returning user: \(savedName), \(savedEmail)")
            }
        }
    }

    func authorizationController(controller: ASAuthorizationController, didCompleteWithError error: Error) {
        print("❌ Sign in failed: \(error.localizedDescription)")
    }
}
```

---

## 👤 Google Sign-In - UIKit

### 1. Import Google SignIn
```swift
import GoogleSignIn
import GoogleSignInSwift
```

### 2. Add Google Sign-In Button
```swift
let googleButton = GIDSignInButton()
googleButton.addTarget(self, action: #selector(handleGoogleSignIn), for: .touchUpInside)
view.addSubview(googleButton)
```

### 3. Handle Sign-In Flow
```swift
@objc func handleGoogleSignIn() {
    guard let clientID = FirebaseApp.app()?.options.clientID else { return }
    let config = GIDConfiguration(clientID: clientID)

    GIDSignIn.sharedInstance.configuration = config
    GIDSignIn.sharedInstance.signIn(withPresenting: self) { result, error in
        guard error == nil, let user = result?.user else {
            print("❌ Google Sign-In Error: \(error?.localizedDescription ?? "Unknown")")
            return
        }

        let name = user.profile?.name ?? "Unknown"
        let email = user.profile?.email ?? "Unknown"
        print("✅ Google User: \(name), \(email)")

        UserDefaults.standard.set(name, forKey: "googleUserName")
        UserDefaults.standard.set(email, forKey: "googleUserEmail")
    }
}
```

---

## 📌 Notes
### Apple:
- `fullName` and `email` only available on **first sign-in**
- Always store them securely for future use
- Use `userIdentifier` to identify users across sessions

### Google:
- Always returns name/email (if user consents)
- Can retrieve profile photo
- Can be integrated with Firebase Auth for unified backend

---

## 🧪 Reset Sign-in for Testing
### Apple:
1. Go to [https://appleid.apple.com](https://appleid.apple.com)
2. Sign in > Security > **Sign in with Apple** > Remove app
3. Reinstall app and try again

### Google:
- Use Incognito or clear Google account permissions at [https://myaccount.google.com/permissions](https://myaccount.google.com/permissions)

---

## 🖼️ Screenshots

> 📸 You can add your Xcode or simulator screenshots here for clarity:

```
[Apple Sign In Button Displayed]
[Google Sign In Button Displayed]
[Console log showing user info]
```

---

## 📚 All Possible Scenarios to Handle
- ✅ First-time user: Store full info
- 🔁 Returning user: Use local/remote profile store
- ❌ Failed login: Show alert, log error
- 🚫 User canceled login: Handle silently or prompt again
- 🔄 Network/credential expired: Retry flow
- 🔐 Secure storage using Keychain if required

---

## 🔐 Want to use Keychain?
Let me know and I’ll help you migrate from `UserDefaults` to `Keychain`.

---

## 📬 Contact
For questions or improvements, feel free to open an issue or reach out.

---

Happy Coding! 🍎☁️
