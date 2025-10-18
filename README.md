
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
    Drag & Drop Images Here or Click to Upload
    <input type="file" id="imageInput" accept="image/*" multiple class="hidden">
  </div>

  <!-- PDF Options -->
  <div class="mb-4 grid grid-cols-2 gap-4">
    <div>
      <label class="block mb-1 font-medium">PDF Page Size:</label>
      <select id="pageSize" class="w-full border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
        <option value="a4">A4</option>
        <option value="letter">Letter</option>
        <option value="custom">Custom (px)</option>
      </select>
    </div>
    <div>
      <label class="block mb-1 font-medium">Image Scale:</label>
      <select id="imgScale" class="w-full border border-gray-300 dark:border-gray-600 rounded px-3 py-2">
        <option value="fit">Fit to Page</option>
        <option value="original">Original Size</option>
      </select>
    </div>
  </div>

  <!-- Image Preview -->
  <div id="preview" class="flex flex-wrap gap-2 mb-4"></div>

  <!-- Convert & Download -->
  <button id="convertBtn" class="bg-blue-600 text-white px-4 py-2 rounded w-full hover:bg-blue-700 transition-colors mb-2">Convert to PDF</button>
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

let selectedFiles = [];
let pdfBlobUrl = null;

// Theme toggle
themeToggle.addEventListener('change', () => {
  document.body.classList.toggle('dark');
});

// Click on drop area to select files
dropArea.addEventListener('click', () => imageInput.click());

// Drag & drop functionality
dropArea.addEventListener('dragover', e => { e.preventDefault(); dropArea.classList.add('border-blue-500'); });
dropArea.addEventListener('dragleave', e => { e.preventDefault(); dropArea.classList.remove('border-blue-500'); });
dropArea.addEventListener('drop', e => {
  e.preventDefault();
  dropArea.classList.remove('border-blue-500');
  addFiles(e.dataTransfer.files);
});

imageInput.addEventListener('change', e => addFiles(e.target.files));

function addFiles(files) {
  for (let file of files) {
    selectedFiles.push(file);
    const imgEl = document.createElement('div');
    imgEl.className = "relative w-20 h-20 overflow-hidden rounded border border-gray-300 dark:border-gray-600";
    imgEl.innerHTML = `
      <img src="${URL.createObjectURL(file)}" class="w-full h-full object-cover" title="${file.name}">
      <button class="absolute top-0 right-0 bg-red-500 text-white rounded-full w-5 h-5 text-xs" title="Remove">×</button>
    `;
    imgEl.querySelector('button').addEventListener('click', () => {
      selectedFiles = selectedFiles.filter(f => f !== file);
      preview.removeChild(imgEl);
    });
    preview.appendChild(imgEl);
  }
}

convertBtn.addEventListener('click', async () => {
  if (selectedFiles.length === 0) {
    alert('Please select at least one image!');
    return;
  }

  status.textContent = 'Generating PDF... Please wait!';
  downloadBtn.classList.add('hidden');

  const pageSize = document.getElementById('pageSize').value;
  const imgScale = document.getElementById('imgScale').value;
  const pdfName = pdfNameInput.value.trim() || 'MyDocument';

  let pdf = new jsPDF({ orientation: 'portrait', unit: 'mm', format: pageSize });

  for (let i = 0; i < selectedFiles.length; i++) {
    const file = selectedFiles[i];
    const imgData = await fileToDataURL(file);
    const img = new Image();
    img.src = imgData;

    await new Promise((resolve) => {
      img.onload = () => {
        let pdfWidth = pdf.internal.pageSize.getWidth() - 20;
        let pdfHeight = pdf.internal.pageSize.getHeight() - 20;
        let imgWidth = img.width;
        let imgHeight = img.height;

        if (imgScale === 'fit') {
          const ratio = Math.min(pdfWidth / imgWidth, pdfHeight / imgHeight);
          imgWidth *= ratio;
          imgHeight *= ratio;
        }

        const x = (pdf.internal.pageSize.getWidth() - imgWidth) / 2;
        const y = (pdf.internal.pageSize.getHeight() - imgHeight) / 2;

        if (i !== 0) pdf.addPage();

        const imgType = file.type === 'image/png' ? 'PNG' : 'JPEG';
        pdf.addImage(img, imgType, x, y, imgWidth, imgHeight);
        status.textContent = `Processing image ${i + 1} of ${selectedFiles.length}...`;
        resolve();
      };
    });
  }

  const pdfBlob = pdf.output('blob');
  if (pdfBlobUrl) URL.revokeObjectURL(pdfBlobUrl);
  pdfBlobUrl = URL.createObjectURL(pdfBlob);

  downloadBtn.href = pdfBlobUrl;
  downloadBtn.download = pdfName + '.pdf';
  downloadBtn.classList.remove('hidden');

  status.textContent = '✅ PDF ready! Click "Download PDF" to save.';
});

function fileToDataURL(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = e => resolve(e.target.result);
    reader.onerror = e => reject(e);
    reader.readAsDataURL(file);
  });
}
</script>
</body>
</html>
