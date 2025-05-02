# Task Detail: FE-CORE-008

## Thông tin chung
- **ID**: FE-CORE-008
- **Tên task**: Xây dựng File Upload/Preview components
- **Độ ưu tiên**: Trung bình
- **Estimate**: 2 days
- **Tham chiếu**: MH-OPP-02 - TAB Tài liệu đính kèm, MH-CTR-02, MH-HRM-02

## Mô tả
Xây dựng bộ components cho việc tải lên, hiển thị preview và quản lý files trong ứng dụng SDIMS. Các components này sẽ được sử dụng trong nhiều phần của ứng dụng như tài liệu đính kèm cho cơ hội, hợp đồng, profiles nhân viên, v.v. Bộ components cần hỗ trợ drag-drop, multiple file upload, progress indicators, và preview cho các loại file phổ biến (PDF, images, Office docs).

## Yêu cầu kỹ thuật
### File Upload Features
- Drag & drop interface cho files
- Multiple file selection
- File type validation
- File size validation
- Progress indicators khi upload
- Retry functionality khi upload fails
- Abort/cancel upload
- Validation errors display

### File Preview Features
- Thumbnail generation/preview cho images
- PDF preview với pagination
- Icon-based previews cho các định dạng khác
- Lightbox/Modal cho xem full-size
- Download options
- Metadata display (file size, type, upload date)

### File Management
- File list với sorting capabilities
- File deletion
- File renaming (nếu cần)
- Version history (nếu cần)
- Permissions/access control

### Integration
- ReactHookForm integration
- API client integration
- Image cropping/resizing tools
- Error handling & retries

## Chi tiết các thành phần

### FileUploader Component
```tsx
// src/components/files/FileUploader.tsx
import React, { useState, useCallback } from 'react';
import { useDropzone, FileRejection } from 'react-dropzone';
import { UploadIcon, XIcon } from '@heroicons/react/outline';
import { cn } from '../../utils/cn';
import { Button } from '../ui/Button';
import { Progress } from '../ui/Progress';

export interface FileUploadProps {
  onUpload: (files: File[]) => Promise<void>;
  maxFiles?: number;
  maxSize?: number; // in bytes
  accept?: Record<string, string[]>;
  className?: string;
  disabled?: boolean;
}

export const FileUploader: React.FC<FileUploadProps> = ({
  onUpload,
  maxFiles = 5,
  maxSize = 5 * 1024 * 1024, // 5MB default
  accept,
  className,
  disabled = false,
}) => {
  const [uploading, setUploading] = useState(false);
  const [progress, setProgress] = useState(0);
  const [error, setError] = useState<string | null>(null);
  
  const handleUpload = useCallback(async (acceptedFiles: File[]) => {
    if (acceptedFiles.length === 0) return;
    
    setUploading(true);
    setProgress(0);
    setError(null);
    
    // Simulate progress with intervals
    const progressInterval = setInterval(() => {
      setProgress(prev => {
        if (prev >= 95) {
          clearInterval(progressInterval);
          return 95;
        }
        return prev + 5;
      });
    }, 100);
    
    try {
      await onUpload(acceptedFiles);
      setProgress(100);
      setTimeout(() => {
        setUploading(false);
        setProgress(0);
      }, 500);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Upload failed');
      setUploading(false);
    } finally {
      clearInterval(progressInterval);
    }
  }, [onUpload]);
  
  const onDrop = useCallback((acceptedFiles: File[], fileRejections: FileRejection[]) => {
    if (fileRejections.length > 0) {
      const errors = fileRejections.map(rejection => {
        if (rejection.errors[0].code === 'file-too-large') {
          return `${rejection.file.name} is too large`;
        }
        if (rejection.errors[0].code === 'file-invalid-type') {
          return `${rejection.file.name} has an invalid file type`;
        }
        return rejection.errors[0].message;
      });
      setError(errors.join(', '));
      return;
    }
    
    handleUpload(acceptedFiles);
  }, [handleUpload]);
  
  const { getRootProps, getInputProps, isDragActive } = useDropzone({
    onDrop,
    maxFiles,
    maxSize,
    accept,
    disabled: disabled || uploading,
  });
  
  return (
    <div className={cn("w-full", className)}>
      <div 
        {...getRootProps()} 
        className={cn(
          "border-2 border-dashed rounded-lg p-6 flex flex-col items-center justify-center cursor-pointer transition-colors",
          isDragActive ? "border-primary-500 bg-primary-50" : "border-gray-300 hover:border-primary-400",
          disabled && "opacity-50 cursor-not-allowed",
          uploading && "pointer-events-none",
          className
        )}
      >
        <input {...getInputProps()} />
        
        <UploadIcon className="w-10 h-10 text-gray-400 mb-3" />
        
        <p className="text-sm text-gray-600">
          {isDragActive 
            ? "Drop the files here..." 
            : "Drag & drop files here, or click to select files"}
        </p>
        
        <p className="text-xs text-gray-500 mt-1">
          {`Max ${maxFiles} files, up to ${(maxSize / (1024 * 1024)).toFixed(0)}MB each`}
        </p>
        
        <Button 
          type="button" 
          variant="outline" 
          size="sm"
          className="mt-3"
          disabled={disabled || uploading}
        >
          Select Files
        </Button>
      </div>
      
      {uploading && (
        <div className="mt-3">
          <div className="flex justify-between text-sm text-gray-500 mb-1">
            <span>Uploading...</span>
            <span>{progress}%</span>
          </div>
          <Progress value={progress} className="w-full" />
        </div>
      )}
      
      {error && (
        <div className="mt-3 p-2 bg-red-50 border border-red-200 rounded text-sm text-red-600 flex items-start">
          <span className="flex-1">{error}</span>
          <button 
            onClick={() => setError(null)}
            className="ml-2 text-red-500 hover:text-red-700"
          >
            <XIcon className="w-4 h-4" />
          </button>
        </div>
      )}
    </div>
  );
};
```

### FilePreview Component
```tsx
// src/components/files/FilePreview.tsx
import React from 'react';
import {
  DocumentIcon,
  PhotographIcon,
  DocumentTextIcon,
  TableIcon,
  FilmIcon,
  MusicNoteIcon,
  CodeIcon,
  ArchiveIcon,
} from '@heroicons/react/outline';
import { cn } from '../../utils/cn';

export interface FileInfo {
  id: string;
  name: string;
  url?: string;
  type: string;
  size: number;
  uploadedAt: Date;
  thumbnailUrl?: string;
}

interface FilePreviewProps {
  file: FileInfo;
  onClick?: (file: FileInfo) => void;
  onRemove?: (file: FileInfo) => void;
  className?: string;
}

export const FilePreview: React.FC<FilePreviewProps> = ({
  file,
  onClick,
  onRemove,
  className,
}) => {
  const { name, type, size, thumbnailUrl } = file;
  
  const isImage = type.startsWith('image/');
  const isPdf = type === 'application/pdf';
  const isVideo = type.startsWith('video/');
  const isAudio = type.startsWith('audio/');
  const isSpreadsheet = [
    'application/vnd.ms-excel',
    'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
    'text/csv',
  ].includes(type);
  const isDocument = [
    'application/msword',
    'application/vnd.openxmlformats-officedocument.wordprocessingml.document',
    'application/vnd.oasis.opendocument.text',
  ].includes(type);
  const isCode = [
    'text/plain',
    'text/html',
    'text/javascript',
    'application/json',
  ].includes(type);
  const isArchive = [
    'application/zip',
    'application/x-rar-compressed',
    'application/x-7z-compressed',
  ].includes(type);
  
  const getFileIcon = () => {
    if (isImage) return <PhotographIcon className="w-10 h-10 text-blue-500" />;
    if (isPdf) return <DocumentTextIcon className="w-10 h-10 text-red-500" />;
    if (isVideo) return <FilmIcon className="w-10 h-10 text-purple-500" />;
    if (isAudio) return <MusicNoteIcon className="w-10 h-10 text-green-500" />;
    if (isSpreadsheet) return <TableIcon className="w-10 h-10 text-emerald-500" />;
    if (isDocument) return <DocumentIcon className="w-10 h-10 text-blue-500" />;
    if (isCode) return <CodeIcon className="w-10 h-10 text-gray-500" />;
    if (isArchive) return <ArchiveIcon className="w-10 h-10 text-amber-500" />;
    return <DocumentIcon className="w-10 h-10 text-gray-500" />;
  };
  
  const formatFileSize = (bytes: number): string => {
    if (bytes < 1024) return `${bytes} B`;
    const kb = bytes / 1024;
    if (kb < 1024) return `${kb.toFixed(1)} KB`;
    const mb = kb / 1024;
    return `${mb.toFixed(1)} MB`;
  };
  
  return (
    <div 
      className={cn(
        "flex border rounded-lg overflow-hidden bg-white transition-shadow hover:shadow-md",
        onClick && "cursor-pointer",
        className
      )}
      onClick={() => onClick?.(file)}
    >
      <div className="w-16 h-16 shrink-0 bg-gray-100 flex items-center justify-center">
        {isImage && thumbnailUrl ? (
          <img
            src={thumbnailUrl}
            alt={name}
            className="w-full h-full object-cover"
          />
        ) : (
          getFileIcon()
        )}
      </div>
      
      <div className="flex-1 p-3 min-w-0">
        <div className="flex items-start justify-between">
          <h3 className="text-sm font-medium text-gray-900 truncate">
            {name}
          </h3>
          {onRemove && (
            <button
              onClick={(e) => {
                e.stopPropagation();
                onRemove(file);
              }}
              className="ml-2 text-gray-400 hover:text-red-500"
            >
              <XIcon className="w-4 h-4" />
            </button>
          )}
        </div>
        <p className="text-xs text-gray-500 mt-1">
          {formatFileSize(size)}
        </p>
      </div>
    </div>
  );
};
```

### FileList Component
```tsx
// src/components/files/FileList.tsx
import React from 'react';
import { FileInfo, FilePreview } from './FilePreview';
import { cn } from '../../utils/cn';

interface FileListProps {
  files: FileInfo[];
  onFileClick?: (file: FileInfo) => void;
  onFileRemove?: (file: FileInfo) => void;
  emptyMessage?: string;
  className?: string;
}

export const FileList: React.FC<FileListProps> = ({
  files,
  onFileClick,
  onFileRemove,
  emptyMessage = "No files uploaded",
  className,
}) => {
  if (files.length === 0) {
    return (
      <div className="text-center py-8 text-gray-500 text-sm">
        {emptyMessage}
      </div>
    );
  }
  
  return (
    <div className={cn("space-y-3", className)}>
      {files.map((file) => (
        <FilePreview
          key={file.id}
          file={file}
          onClick={onFileClick}
          onRemove={onFileRemove}
        />
      ))}
    </div>
  );
};
```

### ImageCropper Component
```tsx
// src/components/files/ImageCropper.tsx
import React, { useState, useRef } from 'react';
import ReactCrop, { Crop } from 'react-image-crop';
import 'react-image-crop/dist/ReactCrop.css';
import { Button } from '../ui/Button';
import { Modal } from '../modal/Modal';
import { ModalHeader } from '../modal/ModalHeader';
import { ModalBody } from '../modal/ModalBody';
import { ModalFooter } from '../modal/ModalFooter';

interface ImageCropperProps {
  src: string;
  isOpen: boolean;
  onClose: () => void;
  onCropComplete: (croppedImageBlob: Blob) => void;
  aspectRatio?: number;
  minWidth?: number;
  minHeight?: number;
}

export const ImageCropper: React.FC<ImageCropperProps> = ({
  src,
  isOpen,
  onClose,
  onCropComplete,
  aspectRatio,
  minWidth = 100,
  minHeight = 100,
}) => {
  const [crop, setCrop] = useState<Crop>({
    unit: '%',
    width: 50,
    height: aspectRatio ? 50 / aspectRatio : 50,
    x: 25,
    y: 25,
  });
  const [completedCrop, setCompletedCrop] = useState<Crop | null>(null);
  const imgRef = useRef<HTMLImageElement>(null);
  
  const getCroppedImg = () => {
    if (!completedCrop || !imgRef.current) return;
    
    const image = imgRef.current;
    const canvas = document.createElement('canvas');
    const scaleX = image.naturalWidth / image.width;
    const scaleY = image.naturalHeight / image.height;
    const ctx = canvas.getContext('2d');
    
    if (!ctx) {
      return;
    }
    
    const pixelRatio = window.devicePixelRatio;
    
    canvas.width = completedCrop.width * scaleX * pixelRatio;
    canvas.height = completedCrop.height * scaleY * pixelRatio;
    
    ctx.setTransform(pixelRatio, 0, 0, pixelRatio, 0, 0);
    ctx.imageSmoothingQuality = 'high';
    
    ctx.drawImage(
      image,
      completedCrop.x * scaleX,
      completedCrop.y * scaleY,
      completedCrop.width * scaleX,
      completedCrop.height * scaleY,
      0,
      0,
      completedCrop.width * scaleX,
      completedCrop.height * scaleY
    );
    
    canvas.toBlob((blob) => {
      if (blob) {
        onCropComplete(blob);
      }
    }, 'image/jpeg', 0.9);
  };
  
  const handleCropComplete = (crop: Crop) => {
    setCompletedCrop(crop);
  };
  
  return (
    <Modal isOpen={isOpen} onClose={onClose} size="lg">
      <ModalHeader>Crop Image</ModalHeader>
      <ModalBody>
        <div className="flex justify-center">
          <ReactCrop
            src={src}
            crop={crop}
            onChange={(c) => setCrop(c)}
            onComplete={handleCropComplete}
            aspect={aspectRatio}
            minWidth={minWidth}
            minHeight={minHeight}
          >
            <img
              ref={imgRef}
              src={src}
              alt="Crop"
              className="max-h-96 object-contain"
            />
          </ReactCrop>
        </div>
      </ModalBody>
      <ModalFooter>
        <Button variant="outline" onClick={onClose}>
          Cancel
        </Button>
        <Button onClick={getCroppedImg}>
          Apply Crop
        </Button>
      </ModalFooter>
    </Modal>
  );
};
```

### FileViewer Component
```tsx
// src/components/files/FileViewer.tsx
import React, { useState } from 'react';
import { Modal } from '../modal/Modal';
import { ModalHeader } from '../modal/ModalHeader';
import { ModalBody } from '../modal/ModalBody';
import { Button } from '../ui/Button';
import { DownloadIcon } from '@heroicons/react/outline';
import { FileInfo } from './FilePreview';
import { Document, Page, pdfjs } from 'react-pdf';

// Configure pdf.js worker path
pdfjs.GlobalWorkerOptions.workerSrc = `//cdnjs.cloudflare.com/ajax/libs/pdf.js/${pdfjs.version}/pdf.worker.min.js`;

interface FileViewerProps {
  file: FileInfo | null;
  isOpen: boolean;
  onClose: () => void;
}

export const FileViewer: React.FC<FileViewerProps> = ({
  file,
  isOpen,
  onClose,
}) => {
  const [numPages, setNumPages] = useState<number | null>(null);
  const [pageNumber, setPageNumber] = useState(1);
  
  if (!file) return null;
  
  const { name, type, url } = file;
  const isImage = type.startsWith('image/');
  const isPdf = type === 'application/pdf';
  
  function onDocumentLoadSuccess({ numPages }: { numPages: number }) {
    setNumPages(numPages);
    setPageNumber(1);
  }
  
  function changePage(offset: number) {
    setPageNumber(prevPageNumber => prevPageNumber + offset);
  }
  
  function previousPage() {
    changePage(-1);
  }
  
  function nextPage() {
    changePage(1);
  }
  
  return (
    <Modal isOpen={isOpen} onClose={onClose} size="xl">
      <ModalHeader className="flex justify-between items-center">
        <span className="truncate">{name}</span>
        {url && (
          <a
            href={url}
            download={name}
            className="flex items-center text-sm text-primary-600 hover:text-primary-800"
          >
            <DownloadIcon className="w-4 h-4 mr-1" />
            Download
          </a>
        )}
      </ModalHeader>
      <ModalBody className="p-0">
        <div className="bg-gray-100 min-h-[400px] flex items-center justify-center p-4">
          {isImage && url && (
            <img
              src={url}
              alt={name}
              className="max-w-full max-h-[600px] object-contain"
            />
          )}
          
          {isPdf && url && (
            <div className="w-full">
              <Document
                file={url}
                onLoadSuccess={onDocumentLoadSuccess}
                className="flex justify-center"
              >
                <Page 
                  pageNumber={pageNumber} 
                  renderTextLayer={false}
                  renderAnnotationLayer={false}
                />
              </Document>
              
              {numPages && numPages > 1 && (
                <div className="flex items-center justify-center mt-4 space-x-4">
                  <Button
                    onClick={previousPage}
                    disabled={pageNumber <= 1}
                    variant="outline"
                    size="sm"
                  >
                    Previous
                  </Button>
                  <p className="text-sm">
                    Page {pageNumber} of {numPages}
                  </p>
                  <Button
                    onClick={nextPage}
                    disabled={pageNumber >= (numPages || 1)}
                    variant="outline"
                    size="sm"
                  >
                    Next
                  </Button>
                </div>
              )}
            </div>
          )}
          
          {!isImage && !isPdf && (
            <div className="text-center p-8">
              <p className="text-gray-500 mb-3">
                Preview not available for this file type
              </p>
              {url && (
                <Button
                  as="a"
                  href={url}
                  target="_blank"
                  rel="noopener noreferrer"
                >
                  Open File
                </Button>
              )}
            </div>
          )}
        </div>
      </ModalBody>
    </Modal>
  );
};
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-004: Theme System (prerequisite)
- FE-INFRA-006: API Client (related - for file uploads)
- FE-CORE-003: Form Components (related - for form integration)
- FE-HRM-003: Employee Form (dependent - uses file upload)
- FE-OPP-002: Opportunity Detail Page (dependent - file attachment tab)

## Tiêu chí chấp nhận
- Drag & drop file upload hoạt động với visual feedback
- Multiple file upload được hỗ trợ
- File type/size validation hoạt động đúng cách
- Progress indicators hiển thị trong quá trình upload
- Error handling và retry functionality hoạt động đúng
- Preview cho images/PDFs hiển thị đúng
- Image cropping tool hoạt động trên các định dạng ảnh phổ biến
- File list hiển thị metadata (size, type, upload date)
- File actions (view, download, delete) hoạt động đúng
- Components responsive trên các kích thước màn hình
- Tất cả components có thể tương thích với React Hook Form
- Documentation đầy đủ về cách sử dụng file components 