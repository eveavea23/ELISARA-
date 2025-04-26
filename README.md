# ELISARA-<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Picture Uploader</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 20px; }
    #gallery img { width: 200px; margin: 10px; object-fit: cover; }
  </style>
</head>
<body>
  <h1>Upload a Picture</h1>
  
  <input type="file" id="fileInput" />
  <button onclick="uploadImage()">Upload</button>
  
  <h2>Gallery</h2>
  <div id="gallery"></div>

  <script>
    async function uploadImage() {
      const fileInput = document.getElementById('fileInput');
      const file = fileInput.files[0];
      if (!file) {
        alert('Please select a file!');
        return;
      }

      // 1. Request a presigned URL from your backend
      const response = await fetch('/get-presigned-url?filename=' + encodeURIComponent(file.name));
      const { url, key } = await response.json();

      // 2. Upload the file directly to S3
      await fetch(url, {
        method: 'PUT',
        headers: {
          'Content-Type': file.type
        },
        body: file
      });

      alert('Upload successful!');

      // 3. Show the uploaded image
      const gallery = document.getElementById('gallery');
      const img = document.createElement('img');
      img.src = `https://YOUR_BUCKET_NAME.s3.YOUR_REGION.amazonaws.com/${key}`;
      gallery.appendChild(img);
    }
  </script>
</body>
</html>
