
# Lunar Client Asset Downloader - downloader.js

The `downloader.js` file contains functions for downloading and saving files from a URL and checking file integrity. This code is a critical part of the Lunar Client Asset Downloader.

## Code Overview

### `downloadAndSaveFile` Function

The `downloadAndSaveFile` function downloads and saves a file from a URL to a specified path. It also allows for optional file integrity checks using SHA-1 or SHA-256 hash algorithms.

```javascript
/**
 * Downloads and saves a file from a URL to the given path
 * @param {string} url - URL of the file to download
 * @param {string} path - Path where to save the file
 * @param {'text'|'blob'} fileType - Type of the file to download
 * @param {string} [hash=null] - SHA-1 or SHA-256 hash of the file to ensure it matches
 * @param {'sha1'|'sha256'} [algorithm='sha1'] - Hash algorithm to use
 * @param {boolean} [logging=true] - Whether or not to log
 * @param {boolean} [skipFolderCheck=false] - Whether or not to check if the folder exists
 */
export async function downloadAndSaveFile(url, path, fileType, hash = null, algorithm = 'sha1', logging = true, skipFolderCheck = false) {
  // Function code
}
```

### `checkHash` Function

The `checkHash` function checks if a given hash matches the hash of a file at a specified path. It uses SHA-1 or SHA-256 for the comparison.

```javascript
/**
 * Checks if the given hash is matching with the given file
 * @param {string} path - Path of the file to check
 * @param {string} hash - Hash to check
 * @param {'sha1'|'sha256'} algorithm - Algorithm to use
 * @param {boolean} [logging=true] - Whether or not to log
 * @returns {Promise<boolean>}
 */
export async function checkHash(path, hash, algorithm, logging = true) {
  // Function code
}
```

### Logging

The code uses a custom logging mechanism through the `Logger` class for informative messages.

```javascript
// Example of creating a logger instance.
const logger = new Logger('downloader');
```

## Usage

To use the functions in `downloader.js`:

1. Import the functions you need.
2. Call `downloadAndSaveFile` to download and save a file from a URL.
3. Call `checkHash` to check the integrity of a file.

Example:

```javascript
import { downloadAndSaveFile, checkHash } from './downloader.js';

// Usage examples
await downloadAndSaveFile(
  'https://example.com/file.txt',
  '/path/to/save/file.txt',
  'text',
  'expectedHash123',
  'sha256',
  true,
  false
);

const isMatching = await checkHash('/path/to/save/file.txt', 'expectedHash123', 'sha256', true);
```

## Links

- [**`downloader.js`**](https://github.com/AlmostGen/Solar-Tweaks-Reborn/blob/main/src/downloader.js)
