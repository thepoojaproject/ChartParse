
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ALL ABOUT PDF - PDF Converter</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdn.tailwindcss.com"></script>
<style>
  body.dark { background-color: #1e293b; color: #f1f5f9; }
  .dark input, .dark select, .dark .slider { background-color: #334155; color: #f1f5f9; }
  .dark #dropArea { color: #f1f5f9; }
  .dark #preview { color: #f1f5f9; }
  .dark #status { color: #86efac; }

  /* Dark mode toggle styles */
  .checkbox { display: none; }
  .slider {
    width: 50px; height: 25px; background-color: lightgray;
    border-radius: 20px; overflow: hidden; display: flex;
    align-items: center; border: 4px solid transparent;
    transition: .3s; box-shadow: 0 0 10px 0 rgb(0 0 0 / 25%) inset;
    cursor: pointer;
  }
  .slider::before {
    content: ''; display: block; width: 100%; height: 100%;
    background-color: #fff; transform: translateX(-25px);
    border-radius: 20px; transition: .3s;
    box-shadow: 0 0 10px 3px rgb(0 0 0 / 25%);
  }
  .checkbox:checked ~ .slider::before { transform: translateX(25px); }
  .checkbox:checked ~ .slider { background-color: #2196F3; }
  .checkbox:active ~ .slider::before { transform: translate(0); }
  
  /* Custom styles for enhanced UI */
  .file-preview {
    transition: all 0.3s ease;
  }
  .file-preview:hover {
    transform: scale(1.05);
  }
  .drop-zone-highlight {
    background-color: rgba(59, 130, 246, 0.1);
    border-color: #3b82f6;
  }
  .loading {
    display: inline-block;
    width: 20px;
    height: 20px;
    border: 3px solid rgba(255,255,255,.3);
    border-radius: 50%;
    border-top-color: #fff;
    animation: spin 1s ease-in-out infinite;
    margin-right: 10px;
  }
  @keyframes spin {
    to { transform: rotate(360deg); }
  }
</style>
</head>
<body class="bg-gray-100 dark:bg-gray-900 flex flex-col items-center justify-center min-h-screen transition-colors duration-500">

<div class="bg-white dark:bg-gray-800 p-6 sm:p-8 rounded-2xl shadow-lg w-full max-w-lg">
  <div class="flex justify-between items-center mb-4">
    <h1 class="text-2xl font-bold text-center w-full">ALL ABOUT PDF</h1>
    <input type="checkbox" id="themeToggle" class="checkbox">
    <label for="themeToggle" class="slider"></label>
  </div>

  <!-- Custom PDF Name -->
  <div class="mb-4">
    <label class="block mb-1 font-medium">PDF Name:</label>
    <input type="text" id="pdfName" placeholder="MyDocument" class="w-full border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
  </div>

  <!-- Drag & Drop Area -->
  <div id="dropArea" class="border-dashed border-4 border-gray-300 dark:border-gray-600 p-6 mb-4 text-center rounded cursor-pointer transition-colors">
    <div class="flex flex-col items-center justify-center">
      <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 text-gray-400 mb-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
      </svg>
      <p class="text-lg font-medium">Drag & Drop Images Here</p>
      <p class="text-sm text-gray-500 dark:text-gray-400 mt-1">or click to upload</p>
      <p class="text-xs text-gray-400 dark:text-gray-500 mt-2">Supports JPG, PNG, GIF, WebP</p>
    </div>
    <input type="file" id="imageInput" accept="image/*" multiple class="hidden">
  </div>

  <!-- File info -->
  <div id="fileInfo" class="mb-4 text-sm text-gray-600 dark:text-gray-400 hidden">
    <span id="fileCount">0</span> files selected • <span id="totalSize">0</span> MB
  </div>

  <!-- PDF Options -->
  <div class="mb-4 grid grid-cols-1 sm:grid-cols-2 gap-4">
    <div>
      <label class="block mb-1 font-medium">PDF Page Size:</label>
      <select id="pageSize" class="w-full border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
        <option value="a4">A4</option>
        <option value="letter">Letter</option>
        <option value="a3">A3</option>
        <option value="a5">A5</option>
        <option value="custom">Custom (px)</option>
      </select>
    </div>
    <div>
      <label class="block mb-1 font-medium">Page Orientation:</label>
      <select id="pageOrientation" class="w-full border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
        <option value="portrait">Portrait</option>
        <option value="landscape">Landscape</option>
      </select>
    </div>
    <div>
      <label class="block mb-1 font-medium">Image Scale:</label>
      <select id="imgScale" class="w-full border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
        <option value="fit">Fit to Page</option>
        <option value="original">Original Size</option>
      </select>
    </div>
    <div id="customSizeContainer" class="hidden">
      <label class="block mb-1 font-medium">Custom Size (px):</label>
      <div class="flex space-x-2">
        <input type="number" id="customWidth" placeholder="Width" class="w-1/2 border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
        <input type="number" id="customHeight" placeholder="Height" class="w-1/2 border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
      </div>
    </div>
  </div>

  <!-- Image Preview -->
  <div id="preview" class="flex flex-wrap gap-2 mb-4 max-h-60 overflow-y-auto p-2 border border-gray-200 dark:border-gray-700 rounded"></div>

  <!-- Convert & Download -->
  <button id="convertBtn" class="bg-blue-600 text-white px-4 py-2 rounded w-full hover:bg-blue-700 transition-colors mb-2 flex items-center justify-center">
    <span id="convertText">Convert to PDF</span>
    <div id="convertSpinner" class="loading hidden"></div>
  </button>
  <a id="downloadBtn" class="bg-green-600 text-white px-4 py-2 rounded w-full hover:bg-green-700 transition-colors hidden text-center block" download>Download PDF</a>

  <!-- Status / Progress -->
  <p id="status" class="mt-4 text-center text-green-600"></p>
</div>

<!-- Footer -->
<p class="text-center text-gray-500 dark:text-gray-400 mt-6 text-sm">
  Made with ❤ by Armeen
</p>

<script>
const { jsPDF } = window.jspdf;
const imageInput = document.getElementById('imageInput');
const dropArea = document.getElementById('dropArea');
const preview = document.getElementById('preview');
const status = document.getElementById('status');
const pdfNameInput = document.getElementById('pdfName');
const themeToggle = document.getElementById('themeToggle');
const convertBtn = document.getElementById('convertBtn');
const downloadBtn = document.getElementById('downloadBtn');
const fileInfo = document.getElementById('fileInfo');
const fileCount = document.getElementById('fileCount');
const totalSize = document.getElementById('totalSize');
const pageSizeSelect = document.getElementById('pageSize');
const customSizeContainer = document.getElementById('customSizeContainer');
const convertText = document.getElementById('convertText');
const convertSpinner = document.getElementById('convertSpinner');

let selectedFiles = [];
let pdfBlobUrl = null;

// Theme toggle
themeToggle.addEventListener('change', () => {
  document.body.classList.toggle('dark');
});

// Show/hide custom size inputs
pageSizeSelect.addEventListener('change', () => {
  if (pageSizeSelect.value === 'custom') {
    customSizeContainer.classList.remove('hidden');
  } else {
    customSizeContainer.classList.add('hidden');
  }
});

// Click on drop area to select files
dropArea.addEventListener('click', () => imageInput.click());

// Drag & drop functionality
dropArea.addEventListener('dragover', e => { 
  e.preventDefault(); 
  dropArea.classList.add('drop-zone-highlight');
});

dropArea.addEventListener('dragleave', e => { 
  e.preventDefault(); 
  dropArea.classList.remove('drop-zone-highlight');
});

dropArea.addEventListener('drop', e => {
  e.preventDefault();
  dropArea.classList.remove('drop-zone-highlight');
  addFiles(e.dataTransfer.files);
});

imageInput.addEventListener('change', e => addFiles(e.target.files));

function updateFileInfo() {
  if (selectedFiles.length === 0) {
    fileInfo.classList.add('hidden');
    return;
  }
  
  fileInfo.classList.remove('hidden');
  fileCount.textContent = selectedFiles.length;
  
  // Calculate total size in MB
  const totalBytes = selectedFiles.reduce((total, file) => total + file.size, 0);
  const totalMB = (totalBytes / (1024 * 1024)).toFixed(2);
  totalSize.textContent = totalMB;
}

function addFiles(files) {
  const validImageTypes = ['image/jpeg', 'image/png', 'image/gif', 'image/webp'];
  
  for (let file of files) {
    // Validate file type
    if (!validImageTypes.includes(file.type)) {
      alert(`"${file.name}" is not a supported image file. Please upload JPG, PNG, GIF, or WebP files.`);
      continue;
    }
    
    // Check file size (limit to 10MB per file)
    if (file.size > 10 * 1024 * 1024) {
      alert(`"${file.name}" is too large. Please upload files smaller than 10MB.`);
      continue;
    }
    
    selectedFiles.push(file);
    const imgEl = document.createElement('div');
    imgEl.className = "file-preview relative w-20 h-24 overflow-hidden rounded border border-gray-300 dark:border-gray-600 bg-gray-50 dark:bg-gray-700";
    imgEl.innerHTML = `
      <img src="${URL.createObjectURL(file)}" class="w-full h-16 object-cover" alt="${file.name}">
      <div class="p-1 text-xs truncate" title="${file.name}">${file.name}</div>
      <button class="absolute top-0 right-0 bg-red-500 text-white rounded-full w-5 h-5 text-xs flex items-center justify-center" title="Remove">×</button>
    `;
    imgEl.querySelector('button').addEventListener('click', (e) => {
      e.stopPropagation();
      selectedFiles = selectedFiles.filter(f => f !== file);
      preview.removeChild(imgEl);
      updateFileInfo();
    });
    preview.appendChild(imgEl);
  }
  
  updateFileInfo();
}

convertBtn.addEventListener('click', async () => {
  if (selectedFiles.length === 0) {
    alert('Please select at least one image!');
    return;
  }

  // Show loading state
  convertText.textContent = 'Converting...';
  convertSpinner.classList.remove('hidden');
  convertBtn.disabled = true;
  
  status.textContent = 'Generating PDF... Please wait!';
  downloadBtn.classList.add('hidden');

  const pageSize = document.getElementById('pageSize').value;
  const imgScale = document.getElementById('imgScale').value;
  const pageOrientation = document.getElementById('pageOrientation').value;
  const pdfName = pdfNameInput.value.trim() || 'MyDocument';

  // Handle custom page size
  let pdfFormat = pageSize;
  if (pageSize === 'custom') {
    const customWidth = parseInt(document.getElementById('customWidth').value);
    const customHeight = parseInt(document.getElementById('customHeight').value);
    
    if (!customWidth || !customHeight || customWidth <= 0 || customHeight <= 0) {
      status.textContent = 'Please enter valid custom dimensions';
      resetConvertButton();
      return;
    }
    
    pdfFormat = [customWidth, customHeight];
  }

  try {
    let pdf = new jsPDF({ 
      orientation: pageOrientation, 
      unit: 'px', 
      format: pdfFormat 
    });

    for (let i = 0; i < selectedFiles.length; i++) {
      const file = selectedFiles[i];
      const imgData = await fileToDataURL(file);
      const img = new Image();
      img.src = imgData;

      await new Promise((resolve, reject) => {
        img.onload = () => {
          try {
            let pageWidth = pdf.internal.pageSize.getWidth();
            let pageHeight = pdf.internal.pageSize.getHeight();
            let imgWidth = img.width;
            let imgHeight = img.height;

            if (imgScale === 'fit') {
              const ratio = Math.min(pageWidth / imgWidth, pageHeight / imgHeight);
              imgWidth *= ratio;
              imgHeight *= ratio;
            }

            const x = (pageWidth - imgWidth) / 2;
            const y = (pageHeight - imgHeight) / 2;

            if (i !== 0) pdf.addPage();

            const imgType = getImageType(file.type);
            pdf.addImage(img, imgType, x, y, imgWidth, imgHeight);
            status.textContent = `Processing image ${i + 1} of ${selectedFiles.length}...`;
            resolve();
          } catch (error) {
            reject(error);
          }
        };
        img.onerror = () => reject(new Error(`Failed to load image: ${file.name}`));
      });
    }

    const pdfBlob = pdf.output('blob');
    if (pdfBlobUrl) URL.revokeObjectURL(pdfBlobUrl);
    pdfBlobUrl = URL.createObjectURL(pdfBlob);

    downloadBtn.href = pdfBlobUrl;
    downloadBtn.download = pdfName + '.pdf';
    downloadBtn.classList.remove('hidden');

    status.textContent = '✅ PDF ready! Click "Download PDF" to save.';
  } catch (error) {
    console.error('PDF generation error:', error);
    status.textContent = `Error: ${error.message}`;
  } finally {
    resetConvertButton();
  }
});

function resetConvertButton() {
  convertText.textContent = 'Convert to PDF';
  convertSpinner.classList.add('hidden');
  convertBtn.disabled = false;
}

function getImageType(mimeType) {
  const typeMap = {
    'image/jpeg': 'JPEG',
    'image/png': 'PNG',
    'image/gif': 'GIF',
    'image/webp': 'WEBP'
  };
  return typeMap[mimeType] || 'JPEG';
}

function fileToDataURL(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = e => resolve(e.target.result);
    reader.onerror = e => reject(e);
    reader.readAsDataURL(file);
  });
}

// Clean up object URLs when page is unloaded
window.addEventListener('beforeunload', () => {
  if (pdfBlobUrl) {
    URL.revokeObjectURL(pdfBlobUrl);
  }
  
  // Clean up image preview URLs
  document.querySelectorAll('#preview img').forEach(img => {
    URL.revokeObjectURL(img.src);
  });
});
</script>
</body>
</html>
