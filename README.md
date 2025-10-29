
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PDF Converter</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdn.tailwindcss.com"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
<style>
  :root {
    --primary: #3b82f6;
    --primary-dark: #1d4ed8;
    --success: #10b981;
    --danger: #ef4444;
    --warning: #f59e0b;
    --custom-bg: #929374;
  }
  
  body {
    transition: background-color 0.3s, color 0.3s;
    background-color: var(--custom-bg);
  }
  
  body.dark {
    background-color: #0f172a;
    color: #e2e8f0;
  }
  
  .card {
    background-color: #f8fafc;
    color: #1e293b;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
  }
  
  .dark .card {
    background-color: #1e293b;
    color: #e2e8f0;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
  }
  
  .dark input, .dark select, .dark textarea {
    background-color: #334155;
    color: #e2e8f0;
    border-color: #475569;
  }
  
  .dark .drop-area {
    border-color: #475569;
    background-color: rgba(51, 65, 85, 0.5);
  }
  
  .dark .drop-area:hover {
    background-color: rgba(51, 65, 85, 0.8);
  }
  
  .checkbox {
    display: none;
  }
  
  .slider {
    width: 60px;
    height: 30px;
    background-color: #cbd5e1;
    border-radius: 30px;
    position: relative;
    cursor: pointer;
    transition: background-color 0.3s;
    box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
  }
  
  .slider::before {
    content: '';
    position: absolute;
    width: 26px;
    height: 26px;
    border-radius: 50%;
    background-color: white;
    top: 2px;
    left: 2px;
    transition: transform 0.3s;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  }
  
  .checkbox:checked + .slider {
    background-color: #3b82f6;
  }
  
  .checkbox:checked + .slider::before {
    transform: translateX(30px);
  }
  
  .drop-area {
    border: 2px dashed #cbd5e1;
    border-radius: 8px;
    padding: 30px;
    text-align: center;
    transition: all 0.3s;
    cursor: pointer;
    background-color: rgba(241, 245, 249, 0.5);
  }
  
  .drop-area:hover {
    background-color: rgba(241, 245, 249, 0.8);
    border-color: #3b82f6;
  }
  
  .drop-area.dragover {
    background-color: rgba(59, 130, 246, 0.1);
    border-color: #3b82f6;
    transform: scale(1.02);
  }
  
  .preview-item {
    position: relative;
    width: 100px;
    height: 100px;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
  .preview-item img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  
  .preview-item .remove-btn {
    position: absolute;
    top: 5px;
    right: 5px;
    width: 24px;
    height: 24px;
    border-radius: 50%;
    background-color: rgba(239, 68, 68, 0.8);
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    opacity: 0;
    transition: opacity 0.3s;
  }
  
  .preview-item:hover .remove-btn {
    opacity: 1;
  }
  
  .progress-bar {
    width: 100%;
    height: 6px;
    background-color: #e2e8f0;
    border-radius: 3px;
    overflow: hidden;
    margin-top: 10px;
  }
  
  .progress-fill {
    height: 100%;
    background-color: #3b82f6;
    width: 0%;
    transition: width 0.3s;
  }
  
  .dark .progress-bar {
    background-color: #475569;
  }
  
  .toast {
    position: fixed;
    bottom: 20px;
    right: 20px;
    padding: 12px 20px;
    border-radius: 8px;
    background-color: #1e293b;
    color: white;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    transform: translateY(100px);
    opacity: 0;
    transition: transform 0.3s, opacity 0.3s;
    z-index: 1000;
  }
  
  .toast.show {
    transform: translateY(0);
    opacity: 1;
  }
  
  .toast.success {
    background-color: #10b981;
  }
  
  .toast.error {
    background-color: #ef4444;
  }
  
  .toast.warning {
    background-color: #f59e0b;
  }
  
  .custom-size-inputs {
    display: none;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
    margin-top: 10px;
  }
  
  #pageSize:checked ~ .custom-size-inputs {
    display: grid;
  }
  
  .tooltip {
    position: relative;
    display: inline-block;
  }
  
  .tooltip .tooltiptext {
    visibility: hidden;
    width: 200px;
    background-color: #1e293b;
    color: #fff;
    text-align: center;
    border-radius: 6px;
    padding: 8px;
    position: absolute;
    z-index: 1;
    bottom: 125%;
    left: 50%;
    transform: translateX(-50%);
    opacity: 0;
    transition: opacity 0.3s;
    font-size: 0.8rem;
  }
  
  .tooltip:hover .tooltiptext {
    visibility: visible;
    opacity: 1;
  }
  
  .dark .tooltip .tooltiptext {
    background-color: #334155;
  }

  .logo-container {
    display: flex;
    justify-content: center;
    margin-bottom: 1rem;
  }
  
  .logo {
    max-width: 200px;
    height: auto;
  }
</style>
</head>
<body class="transition-colors duration-500">
<div class="container mx-auto px-4 py-8">
  <div class="max-w-3xl mx-auto">
    <!-- Header -->
    <div class="card rounded-2xl p-6 mb-6">
      <div class="logo-container">
        <img src="https://i.ibb.co/4nSDnMgN/image.png" alt="PDF Converter Logo" class="logo">
      </div>
      <div class="flex justify-between items-center mb-2">
        <div class="flex-1"></div>
        <div class="flex items-center space-x-2">
          <span class="text-sm"><i class="fas fa-moon"></i></span>
          <input type="checkbox" id="themeToggle" class="checkbox">
          <label for="themeToggle" class="slider"></label>
          <span class="text-sm"><i class="fas fa-sun"></i></span>
        </div>
      </div>
      <p class="text-center text-gray-600 dark:text-gray-400">Convert your images to PDF with ease</p>
    </div>

    <!-- Main Content -->
    <div class="card rounded-2xl p-6 mb-6">
      <!-- PDF Name -->
      <div class="mb-6">
        <label class="block mb-2 font-medium text-gray-700 dark:text-gray-300">
          <i class="fas fa-file-signature mr-2"></i>PDF Name
        </label>
        <input type="text" id="pdfName" placeholder="MyDocument" 
               class="w-full border border-gray-300 dark:border-gray-600 rounded-lg px-4 py-3 focus:outline-none focus:ring-2 focus:ring-blue-500">
      </div>

      <!-- Drag & Drop Area -->
      <div class="mb-6">
        <label class="block mb-2 font-medium text-gray-700 dark:text-gray-300">
          <i class="fas fa-images mr-2"></i>Upload Images
        </label>
        <div id="dropArea" class="drop-area">
          <div class="flex flex-col items-center justify-center">
            <i class="fas fa-cloud-upload-alt text-4xl text-gray-400 mb-2"></i>
            <p class="text-lg font-medium">Drag & Drop Images Here</p>
            <p class="text-gray-500 dark:text-gray-400 mt-1">or</p>
            <button id="uploadBtn" class="mt-2 bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded-lg transition-colors">
              <i class="fas fa-folder-open mr-2"></i>Browse Files
            </button>
            <input type="file" id="imageInput" accept="image/*" multiple class="hidden">
          </div>
        </div>
      </div>

      <!-- Image Preview -->
      <div class="mb-6">
        <label class="block mb-2 font-medium text-gray-700 dark:text-gray-300">
          <i class="fas fa-eye mr-2"></i>Image Preview
          <span id="imageCount" class="text-sm text-gray-500 ml-2">(0 images)</span>
        </label>
        <div id="preview" class="flex flex-wrap gap-4">
          <div id="emptyPreview" class="w-full text-center py-8 text-gray-500 dark:text-gray-400">
            <i class="fas fa-image text-4xl mb-2"></i>
            <p>No images uploaded yet</p>
          </div>
        </div>
      </div>

      <!-- PDF Options -->
      <div class="mb-6">
        <label class="block mb-2 font-medium text-gray-700 dark:text-gray-300">
          <i class="fas fa-cog mr-2"></i>PDF Options
        </label>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <!-- Page Size -->
          <div>
            <label class="block mb-1 text-gray-600 dark:text-gray-400">Page Size</label>
            <select id="pageSize" class="w-full border border-gray-300 dark:border-gray-600 rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500">
              <option value="a4">A4</option>
              <option value="letter">Letter</option>
              <option value="legal">Legal</option>
              <option value="custom">Custom Size</option>
            </select>
            
            <!-- Custom Size Inputs -->
            <div class="custom-size-inputs mt-2">
              <div>
                <label class="block mb-1 text-gray-600 dark:text-gray-400">Width (mm)</label>
                <input type="number" id="customWidth" min="50" max="500" value="210" class="w-full border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
              </div>
              <div>
                <label class="block mb-1 text-gray-600 dark:text-gray-400">Height (mm)</label>
                <input type="number" id="customHeight" min="50" max="500" value="297" class="w-full border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
              </div>
            </div>
          </div>
          
          <!-- Image Scale -->
          <div>
            <label class="block mb-1 text-gray-600 dark:text-gray-400">Image Scale</label>
            <select id="imgScale" class="w-full border border-gray-300 dark:border-gray-600 rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500">
              <option value="fit">Fit to Page</option>
              <option value="original">Original Size</option>
              <option value="custom">Custom Scale</option>
            </select>
            
            <!-- Custom Scale Input -->
            <div id="customScaleContainer" class="mt-2 hidden">
              <label class="block mb-1 text-gray-600 dark:text-gray-400">Scale Factor (0.1 - 2.0)</label>
              <input type="number" id="customScale" min="0.1" max="2.0" step="0.1" value="1.0" class="w-full border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
            </div>
          </div>
        </div>
        
        <!-- Orientation and Margins -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-4">
          <!-- Orientation -->
          <div>
            <label class="block mb-1 text-gray-600 dark:text-gray-400">Orientation</label>
            <select id="pageOrientation" class="w-full border border-gray-300 dark:border-gray-600 rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500">
              <option value="portrait">Portrait</option>
              <option value="landscape">Landscape</option>
            </select>
          </div>
          
          <!-- Margins -->
          <div>
            <label class="block mb-1 text-gray-600 dark:text-gray-400">
              Margin (mm)
              <span class="tooltip ml-1">
                <i class="fas fa-info-circle text-blue-500"></i>
                <span class="tooltiptext">Adds space around the images in the PDF</span>
              </span>
            </label>
            <input type="number" id="pageMargin" min="0" max="50" value="10" class="w-full border border-gray-300 dark:border-gray-600 rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </div>
        </div>
      </div>

      <!-- Progress Bar -->
      <div class="mb-6 hidden" id="progressContainer">
        <div class="flex justify-between mb-1">
          <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Conversion Progress</span>
          <span id="progressPercent" class="text-sm font-medium text-gray-700 dark:text-gray-300">0%</span>
        </div>
        <div class="progress-bar">
          <div id="progressFill" class="progress-fill"></div>
        </div>
      </div>

      <!-- Action Buttons -->
      <div class="flex flex-col sm:flex-row gap-3">
        <button id="convertBtn" class="flex-1 bg-blue-600 hover:bg-blue-700 text-white px-6 py-3 rounded-lg font-medium transition-colors flex items-center justify-center">
          <i class="fas fa-file-pdf mr-2"></i>Convert to PDF
        </button>
        <a id="downloadBtn" class="flex-1 bg-green-600 hover:bg-green-700 text-white px-6 py-3 rounded-lg font-medium transition-colors hidden text-center flex items-center justify-center" download>
          <i class="fas fa-download mr-2"></i>Download PDF
        </a>
        <button id="resetBtn" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white px-6 py-3 rounded-lg font-medium transition-colors flex items-center justify-center">
          <i class="fas fa-redo mr-2"></i>Reset
        </button>
      </div>

      <!-- Status Message -->
      <div id="status" class="mt-4 text-center text-green-600 dark:text-green-400 font-medium"></div>
    </div>

    <!-- Footer -->
    <p class="text-center text-gray-700 dark:text-gray-300 mt-6 text-sm">
      Made with <i class="fas fa-heart text-red-500"></i> by Armeen
    </p>
  </div>
</div>

<!-- Toast Notification -->
<div id="toast" class="toast">
  <div class="flex items-center">
    <i id="toastIcon" class="fas fa-check-circle mr-2"></i>
    <span id="toastMessage">Operation completed successfully!</span>
  </div>
</div>

<script>
const { jsPDF } = window.jspdf;
const imageInput = document.getElementById('imageInput');
const dropArea = document.getElementById('dropArea');
const preview = document.getElementById('preview');
const emptyPreview = document.getElementById('emptyPreview');
const status = document.getElementById('status');
const pdfNameInput = document.getElementById('pdfName');
const themeToggle = document.getElementById('themeToggle');
const convertBtn = document.getElementById('convertBtn');
const downloadBtn = document.getElementById('downloadBtn');
const resetBtn = document.getElementById('resetBtn');
const uploadBtn = document.getElementById('uploadBtn');
const imageCount = document.getElementById('imageCount');
const progressContainer = document.getElementById('progressContainer');
const progressFill = document.getElementById('progressFill');
const progressPercent = document.getElementById('progressPercent');
const pageSizeSelect = document.getElementById('pageSize');
const imgScaleSelect = document.getElementById('imgScale');
const customScaleContainer = document.getElementById('customScaleContainer');
const customScaleInput = document.getElementById('customScale');
const pageOrientationSelect = document.getElementById('pageOrientation');
const pageMarginInput = document.getElementById('pageMargin');
const customWidthInput = document.getElementById('customWidth');
const customHeightInput = document.getElementById('customHeight');
const toast = document.getElementById('toast');
const toastIcon = document.getElementById('toastIcon');
const toastMessage = document.getElementById('toastMessage');

let selectedFiles = [];
let pdfBlobUrl = null;

// Initialize theme based on system preference or stored preference
function initializeTheme() {
  const savedTheme = localStorage.getItem('theme');
  const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
  
  if (savedTheme === 'dark' || (!savedTheme && systemPrefersDark)) {
    document.body.classList.add('dark');
    themeToggle.checked = true;
  }
}

// Show toast notification
function showToast(message, type = 'success') {
  toastMessage.textContent = message;
  toast.className = 'toast show ' + type;
  
  // Set icon based on type
  if (type === 'success') {
    toastIcon.className = 'fas fa-check-circle mr-2';
  } else if (type === 'error') {
    toastIcon.className = 'fas fa-exclamation-circle mr-2';
  } else if (type === 'warning') {
    toastIcon.className = 'fas fa-exclamation-triangle mr-2';
  }
  
  // Hide after 3 seconds
  setTimeout(() => {
    toast.classList.remove('show');
  }, 3000);
}

// Update image count
function updateImageCount() {
  const count = selectedFiles.length;
  imageCount.textContent = `(${count} image${count !== 1 ? 's' : ''})`;
}

// Theme toggle
themeToggle.addEventListener('change', () => {
  document.body.classList.toggle('dark');
  localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light');
});

// Click on drop area or upload button to select files
uploadBtn.addEventListener('click', () => imageInput.click());
dropArea.addEventListener('click', () => imageInput.click());

// Drag & drop functionality
dropArea.addEventListener('dragover', e => {
  e.preventDefault();
  dropArea.classList.add('dragover');
});

dropArea.addEventListener('dragleave', e => {
  e.preventDefault();
  dropArea.classList.remove('dragover');
});

dropArea.addEventListener('drop', e => {
  e.preventDefault();
  dropArea.classList.remove('dragover');
  addFiles(e.dataTransfer.files);
});

imageInput.addEventListener('change', e => addFiles(e.target.files));

// Show/hide custom scale input
imgScaleSelect.addEventListener('change', () => {
  if (imgScaleSelect.value === 'custom') {
    customScaleContainer.classList.remove('hidden');
  } else {
    customScaleContainer.classList.add('hidden');
  }
});

// Show/hide custom size inputs
pageSizeSelect.addEventListener('change', () => {
  const customSizeInputs = document.querySelector('.custom-size-inputs');
  if (pageSizeSelect.value === 'custom') {
    customSizeInputs.style.display = 'grid';
  } else {
    customSizeInputs.style.display = 'none';
  }
});

function addFiles(files) {
  if (files.length === 0) return;
  
  // Check if any files are not images
  const nonImageFiles = Array.from(files).filter(file => !file.type.startsWith('image/'));
  if (nonImageFiles.length > 0) {
    showToast('Only image files are supported!', 'error');
    return;
  }
  
  // Add files to the list
  for (let file of files) {
    selectedFiles.push(file);
    
    // Create preview element
    const previewItem = document.createElement('div');
    previewItem.className = 'preview-item';
    
    const img = document.createElement('img');
    img.src = URL.createObjectURL(file);
    img.alt = file.name;
    
    const removeBtn = document.createElement('div');
    removeBtn.className = 'remove-btn';
    removeBtn.innerHTML = '<i class="fas fa-times"></i>';
    removeBtn.title = 'Remove image';
    
    removeBtn.addEventListener('click', (e) => {
      e.stopPropagation();
      selectedFiles = selectedFiles.filter(f => f !== file);
      preview.removeChild(previewItem);
      updateImageCount();
      
      // Show empty message if no images
      if (selectedFiles.length === 0) {
        preview.appendChild(emptyPreview);
      }
      
      showToast('Image removed', 'warning');
    });
    
    previewItem.appendChild(img);
    previewItem.appendChild(removeBtn);
    
    // Remove empty preview message if it's the first image
    if (selectedFiles.length === 1 && emptyPreview.parentNode) {
      preview.removeChild(emptyPreview);
    }
    
    preview.appendChild(previewItem);
  }
  
  updateImageCount();
  showToast(`${files.length} image${files.length !== 1 ? 's' : ''} added successfully`, 'success');
}

// Convert to PDF
convertBtn.addEventListener('click', async () => {
  if (selectedFiles.length === 0) {
    showToast('Please select at least one image!', 'error');
    return;
  }

  status.textContent = '';
  downloadBtn.classList.add('hidden');
  progressContainer.classList.remove('hidden');
  progressFill.style.width = '0%';
  progressPercent.textContent = '0%';
  convertBtn.disabled = true;

  const pdfName = pdfNameInput.value.trim() || 'MyDocument';
  const pageSize = pageSizeSelect.value;
  const imgScale = imgScaleSelect.value;
  const customScale = parseFloat(customScaleInput.value) || 1.0;
  const orientation = pageOrientationSelect.value;
  const margin = parseInt(pageMarginInput.value) || 10;
  
  let format;
  if (pageSize === 'custom') {
    format = [parseFloat(customWidthInput.value), parseFloat(customHeightInput.value)];
  } else {
    format = pageSize;
  }

  let pdf = new jsPDF({
    orientation: orientation,
    unit: 'mm',
    format: format
  });

  try {
    for (let i = 0; i < selectedFiles.length; i++) {
      const file = selectedFiles[i];
      const imgData = await fileToDataURL(file);
      const img = new Image();
      img.src = imgData;

      await new Promise((resolve, reject) => {
        img.onload = () => {
          try {
            let pdfWidth = pdf.internal.pageSize.getWidth() - (2 * margin);
            let pdfHeight = pdf.internal.pageSize.getHeight() - (2 * margin);
            let imgWidth = img.width;
            let imgHeight = img.height;

            // Calculate scaling based on selected option
            if (imgScale === 'fit') {
              const ratio = Math.min(pdfWidth / imgWidth, pdfHeight / imgHeight);
              imgWidth *= ratio;
              imgHeight *= ratio;
            } else if (imgScale === 'custom') {
              imgWidth *= customScale;
              imgHeight *= customScale;
            }
            // For 'original', we keep the original dimensions

            const x = margin + (pdfWidth - imgWidth) / 2;
            const y = margin + (pdfHeight - imgHeight) / 2;

            if (i !== 0) pdf.addPage();

            const imgType = file.type === 'image/png' ? 'PNG' : 'JPEG';
            pdf.addImage(img, imgType, x, y, imgWidth, imgHeight);
            
            // Update progress
            const progress = ((i + 1) / selectedFiles.length) * 100;
            progressFill.style.width = `${progress}%`;
            progressPercent.textContent = `${Math.round(progress)}%`;
            
            status.textContent = `Processing image ${i + 1} of ${selectedFiles.length}...`;
            resolve();
          } catch (error) {
            reject(error);
          }
        };
        
        img.onerror = () => {
          reject(new Error(`Failed to load image: ${file.name}`));
        };
      });
    }

    const pdfBlob = pdf.output('blob');
    if (pdfBlobUrl) URL.revokeObjectURL(pdfBlobUrl);
    pdfBlobUrl = URL.createObjectURL(pdfBlob);

    downloadBtn.href = pdfBlobUrl;
    downloadBtn.download = pdfName + '.pdf';
    downloadBtn.classList.remove('hidden');
    progressContainer.classList.add('hidden');
    
    showToast('PDF created successfully!', 'success');
    status.textContent = '✅ PDF ready! Click "Download PDF" to save.';
  } catch (error) {
    console.error('PDF generation error:', error);
    showToast('Error generating PDF: ' + error.message, 'error');
    status.textContent = '❌ Error generating PDF. Please try again.';
    progressContainer.classList.add('hidden');
  } finally {
    convertBtn.disabled = false;
  }
});

// Reset button
resetBtn.addEventListener('click', () => {
  selectedFiles = [];
  preview.innerHTML = '';
  preview.appendChild(emptyPreview);
  updateImageCount();
  pdfNameInput.value = '';
  status.textContent = '';
  downloadBtn.classList.add('hidden');
  progressContainer.classList.add('hidden');
  
  if (pdfBlobUrl) {
    URL.revokeObjectURL(pdfBlobUrl);
    pdfBlobUrl = null;
  }
  
  showToast('All settings have been reset', 'warning');
});

function fileToDataURL(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = e => resolve(e.target.result);
    reader.onerror = e => reject(e);
    reader.readAsDataURL(file);
  });
}

// Initialize the app
initializeTheme();
</script>
</body>
</html>
