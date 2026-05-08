# MIT App Inventor Integration Guide

## Overview
This guide shows how to connect your HTML Login Portal to MIT App Inventor.

## Step 1: Prepare the HTML Portal for Communication

Your HTML file needs to communicate with MIT App Inventor. We have two options:

### Option A: Host on Web Server (Recommended)
1. Upload `index.html.html` to a web hosting service (Firebase, Netlify, GitHub Pages, etc.)
2. Note the public URL (e.g., `https://yourdomain.com/index.html`)

### Option B: Local Network (Advanced)
1. Use a local web server on your computer
2. Configure port forwarding if accessing from mobile device

---

## Step 2: Create MIT App Inventor Project

### Screen Setup

1. **Open MIT App Inventor** (http://ai2.appinventor.mit.edu)
2. Create a new project named "LoginApp"

### Add Components:

#### Layout (Screen1)
- **VerticalArrangement** (main container)
  - **Label** - Title: "Student Login"
  - **TextBox** (username_input) - Hint: "Enter username"
  - **TextBox** (password_input) - Input Type: Password, Hint: "Enter password"
  - **Button** (login_button) - Text: "Sign In"
  - **Label** (message_label) - Text: "" (empty)
  - **WebViewer** (web_viewer) - Visible: False (optional, for displaying HTML)

#### Non-Visible Components
- **Web1** (WebComponent) - for HTTP requests

---

## Step 3: Blocks Programming

### Key Blocks Needed:

**1. When login_button.Click**
```
- Get username from username_input
- Get password from password_input
- Call Web1.PostText (to your server)
- Pass: username and password as parameters
```

**2. Web1.GotText**
```
- Check response from server
- If valid: show success message
- If invalid: show error message
- Lock account after 3 attempts
```

**3. Password Toggle (Optional)**
```
- When toggle_button.Click
- Change password_input.PasswordMode between True/False
```

---

## Step 4: Backend Setup

You need a backend to validate credentials. Options:

### Option 1: Firebase (Free & Easy)
Create a Firebase function that:
- Receives username and password
- Validates against your users list
- Returns JSON response: `{ "success": true/false, "message": "..." }`

### Option 2: PHP Backend
```php
<?php
$username = $_POST['username'];
$password = $_POST['password'];

$users = [
    ["username" => "admin", "password" => "1234"],
    ["username" => "student1", "password" => "1111"],
    ["username" => "student2", "password" => "2222"]
];

$valid = false;
foreach ($users as $user) {
    if ($user['username'] === $username && $user['password'] === $password) {
        $valid = true;
        break;
    }
}

header('Content-Type: application/json');
echo json_encode([
    "success" => $valid,
    "message" => $valid ? "Login successful" : "Invalid credentials"
]);
?>
```

### Option 3: Google Apps Script
Deploy a web app that validates credentials and returns JSON.

---

## Step 5: Complete Block Code Logic

### Main Login Flow:

```
When login_button.Click:
  ├─ Store username = username_input.Text
  ├─ Store password = password_input.Text
  ├─ Disable login_button
  ├─ Call Web1.PostText to backend
  │  └─ URL: "https://your-backend.com/login"
  │  └─ Text: {"username": username, "password": password}
  └─ Wait for response...

When Web1.GotText(response):
  ├─ Parse JSON response
  ├─ If response.success = true:
  │  ├─ Show "✅ Login successful!"
  │  ├─ Disable inputs
  │  └─ Navigate to dashboard
  ├─ Else:
  │  ├─ Increment attempts
  │  ├─ Show "❌ Invalid credentials"
  │  ├─ If attempts >= 3:
  │  │  └─ Lock account & disable inputs
  │  └─ Re-enable login_button
```

---

## Step 6: Export & Test

1. **For Testing**: Build APK for Android
2. **For iOS**: Use Companion app
3. **Test with credentials**:
   - Username: `admin`, Password: `1234`
   - Username: `student1`, Password: `1111`
   - Username: `student2`, Password: `2222`

---

## Alternative: Direct HTML Integration

If you want to embed the HTML directly in MIT App Inventor:

1. Add **WebViewer** component to Screen
2. Set `WebViewer1.HomeURL` to your hosted HTML URL
3. Use **JavaScript Bridge** to communicate between App and HTML:

```javascript
// In HTML file, add:
window.AppInventor.setWebViewString('loginResult', JSON.stringify({
    success: true,
    username: user
}));
```

---

## File Upload to Server

Upload your `index.html.html` to:
- **GitHub Pages**: Push to `gh-pages` branch
- **Firebase Hosting**: Use Firebase CLI
- **Netlify**: Drag and drop file
- **Replit**: Create web project and paste code

Then use the public URL in MIT App Inventor's Web component.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| CORS errors | Use backend API instead of direct calls |
| HTML not displaying | Check WebViewer.HomeURL is correct |
| Credentials not matching | Verify backend validation logic |
| App crashes on login | Add error handling in Web1.GotError block |

---

## Next Steps

1. Choose hosting option (Firebase, Netlify, etc.)
2. Upload HTML file
3. Set up backend API
4. Build MIT App Inventor blocks
5. Test on Android device or Companion app
