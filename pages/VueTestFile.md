---
  title: 'VueTestFile.vue'
  description: 'component description'
  pubDate: 'July 29, 2024'
  author: 'Carlos P'
  ---
  
  
  
  # VueTestFile.vue
  ## Explanation of the Code

### Purpose:
The provided code is a Vue.js component that allows users to drag and drop files or select files from their system. It enforces restrictions on the types of files that can be uploaded and limits the number of files that can be selected.

### Components Used:
- **DocumentTextIcon**: A component imported from the `@heroicons/vue` library that represents an icon for a document file.
- **ArrowUpTrayIcon**: A component imported from the `@heroicons/vue` library that represents an icon for an arrow pointing upwards.

### Data:
- **allowedExtensions**: An array containing a list of file extensions that are allowed to be uploaded.
- **selectedFiles**: An array that stores the files selected by the user.
- **maxFiles**: A variable that defines the maximum number of files that can be selected.

### Methods:
1. **handleDragOver(event)**:
   - Prevents the default behavior of the dragover event.

2. **handleDrop(event)**:
   - Extracts files from the dropped items and validates them before setting the selected files.

3. **extractFiles(items)**:
   - Extracts files from the dropped items or directories recursively and validates each file against the allowed extensions.

4. **traverseFileTree(item, filesArray)**:
   - Recursively traverses a directory to extract files and add them to the filesArray.

5. **isValidFile(file)**:
   - Checks if a file has a valid extension based on the allowedExtensions array.

6. **handleFileSelect(event)**:
   - Handles the event when files are selected using the file input and validates the selected files.

7. **validateAndSetFiles(files)**:
   - Validates the selected files against the allowed extensions and the maximum number of files before setting them in the selectedFiles array.

### Example Usage:
```html
<template>
  <!-- Vue component template code -->
</template>

<script>
import { DocumentTextIcon, ArrowUpTrayIcon } from "@heroicons/vue/24/outline";

export default {
  components: {
    DocumentTextIcon,
    ArrowUpTrayIcon
  },
  data() {
    return {
      allowedExtensions: [".js", ".jsx", ".ts", ".tsx", ".py", ".java", ".rb", ".php",
                          ".html", ".css", ".cpp", ".c", ".go", ".rs", ".swift", ".kt",
                          ".m", ".h", ".cs", ".json", ".xml", ".sh", ".yml", ".yaml",
                          ".vue", ".svelte", ".qwik"],
      selectedFiles: [],
      maxFiles: 4
    };
  },
  methods: {
    // Methods explained above
  }
};
</script>

<style scoped>
/* Component-specific styles */
</style>
```

In this example, the Vue component template includes a draggable area where users can drop files or click to select files. The component enforces restrictions on file types and the maximum number of files that can be uploaded.
  
  ## Component Code
  ```jsx
  ///jsx
  <template>
    <div
      @dragover.prevent="handleDragOver"
      @drop.prevent="handleDrop"
      class="p-4 border-2 border-dashed border-gray-300 rounded-md text-center cursor-pointer mb-4 h-96 w-96 flex overflow-y-scroll items-center justify-center"
    >
      <input
        type="file"
        @change="handleFileSelect"
        style="display: none;"
        :accept="allowedExtensions.join(',')"
        id="fileUpload"
        multiple
      />
      <label for="fileUpload" class="cursor-pointer">
        <div v-if="selectedFiles.length">
          <div class="flex flex-wrap gap-10">
            <div v-for="file in selectedFiles" :key="file.name">
              <DocumentTextIcon class="mx-auto w-8" />
              <p>{{ file.name }}</p>
            </div>
          </div>
        </div>
        <div v-else>
          <ArrowUpTrayIcon class="mx-auto w-8" />
          <p>Drag & drop files or folders here, or click to select files</p>
        </div>
      </label>
    </div>
  </template>
  
  <script>
  import { DocumentTextIcon, ArrowUpTrayIcon } from "@heroicons/vue/24/outline";
  
  export default {
    components: {
      DocumentTextIcon,
      ArrowUpTrayIcon
    },
    data() {
      return {
        allowedExtensions: [
          ".js", ".jsx", ".ts", ".tsx", ".py", ".java", ".rb", ".php",
          ".html", ".css", ".cpp", ".c", ".go", ".rs", ".swift", ".kt",
          ".m", ".h", ".cs", ".json", ".xml", ".sh", ".yml", ".yaml",
          ".vue", ".svelte", ".qwik"
        ],
        selectedFiles: [],
        maxFiles: 4
      };
    },
    methods: {
      handleDragOver(event) {
        event.preventDefault();
      },
      handleDrop(event) {
        const items = event.dataTransfer.items;
        this.extractFiles(items).then(files => this.validateAndSetFiles(files));
      },
      async extractFiles(items) {
        const filesArray = [];
        for (let i = 0; i < items.length; i++) {
          const item = items[i].webkitGetAsEntry();
          if (item.isFile) {
            const file = await new Promise(resolve => item.file(resolve));
            if (this.isValidFile(file)) {
              filesArray.push(file);
            } else {
              this.$emit("setError", `Invalid file type. Only ${this.allowedExtensions.join(", ")} files are allowed.`);
            }
          } else if (item.isDirectory) {
            await this.traverseFileTree(item, filesArray);
          }
        }
        return filesArray;
      },
      traverseFileTree(item, filesArray) {
        return new Promise(resolve => {
          if (item.isFile) {
            item.file(file => {
              if (this.isValidFile(file)) {
                filesArray.push(file);
              }
              resolve();
            });
          } else if (item.isDirectory) {
            const dirReader = item.createReader();
            dirReader.readEntries(async entries => {
              for (const entry of entries) {
                await this.traverseFileTree(entry, filesArray);
              }
              resolve();
            });
          }
        });
      },
      isValidFile(file) {
        return this.allowedExtensions.some(ext => file.name.endsWith(ext));
      },
      handleFileSelect(event) {
        const files = Array.from(event.target.files);
        this.validateAndSetFiles(files);
      },
      validateAndSetFiles(files) {
        const filteredFiles = files.filter(file => this.isValidFile(file));
        if (filteredFiles.length > this.maxFiles) {
          this.$emit("setError", `You can only upload a maximum of ${this.maxFiles} files.`);
        } else {
          this.selectedFiles = filteredFiles.slice(0, this.maxFiles);
          this.$emit("setError", "");
        }
      }
    }
  };
  </script>
  
  <style scoped>
  /* Add any component-specific styles here */
  </style>
  ///
  ```
  