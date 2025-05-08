# Sign in with Apple - UIKit (iOS)

This project demonstrates how to implement **Sign in with Apple** using **UIKit** and fetch user information (Full Name, Email) on first login.

---

## ✅ Features
- Apple Sign-in button (UIKit)
- Full name and email retrieval (on first login only)
- User info storage using `UserDefaults`
- User identification via `userIdentifier`

---

## 🧱 Requirements
- iOS 13+
- Xcode 11+
- Apple Developer Account (with Sign in with Apple enabled)

---

## 🔧 Setup Instructions

### 1. Enable Sign in with Apple
- Go to [Apple Developer Portal](https://developer.apple.com/account/)
- Select your App ID > Enable **Sign in with Apple**
- In Xcode, go to **Signing & Capabilities** > Add **"Sign in with Apple"**

---

### 2. Import Authentication Services
```swift
import AuthenticationServices
```

---

### 3. Add Sign in with Apple Button
```swift
let appleButton = ASAuthorizationAppleIDButton()
appleButton.addTarget(self, action: #selector(handleAppleSignIn), for: .touchUpInside)
view.addSubview(appleButton)

// Constraints
appleButton.translatesAutoresizingMaskIntoConstraints = false
NSLayoutConstraint.activate([
    appleButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
    appleButton.centerYAnchor.constraint(equalTo: view.centerYAnchor),
    appleButton.heightAnchor.constraint(equalToConstant: 50),
    appleButton.widthAnchor.constraint(equalToConstant: 280)
])
```

---

### 4. Handle Sign in Logic
```swift
@objc func handleAppleSignIn() {
    let provider = ASAuthorizationAppleIDProvider()
    let request = provider.createRequest()
    request.requestedScopes = [.fullName, .email]

    let controller = ASAuthorizationController(authorizationRequests: [request])
    controller.delegate = self
    controller.presentationContextProvider = self
    controller.performRequests()
}
```

---

### 5. Handle Delegate Methods
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

                // Store
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

## 📌 Notes
- `fullName` and `email` are **only available on first login**.
- Use `userIdentifier` to identify and manage returning users.
- Store name/email locally or send them to your backend.

---

## 🧪 Reset Sign-in for Testing
To test full name/email again:
1. Go to [https://appleid.apple.com](https://appleid.apple.com)
2. Sign in > Security > **Sign in with Apple**
3. Remove your app
4. Reinstall or retry Sign in

---

## 🖼️ Screenshots

> 📸 You can add your Xcode or simulator screenshots here for clarity:

```
[Apple Sign In Button Displayed]
[Console log showing first-time user name/email]
```

---

## 🔐 Want to use Keychain?
Let me know and I’ll help you migrate from `UserDefaults` to `Keychain`.

---

## 📬 Contact
For questions or improvements, feel free to open an issue or reach out.

---

Happy Coding! 🍎
