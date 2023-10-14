
# Lunar Client Asset Downloader - engine.js

The `engine.js` file contains functions for verifying the existence of an Engine and its related files. It also handles the process of downloading and saving the Engine if necessary. This code is a vital component of the Lunar Client Asset Downloader.

## Code Overview

### `verifyEngine` Function

The `verifyEngine` function ensures the Engine and its related files exist and are up to date. It performs tasks such as creating or updating configuration files, fetching the latest updater index, and downloading the Engine.

```javascript
/**
 * Verify the Engine and all files it needs exist
 * @returns {Promise<void>}
 */
export async function verifyEngine() {
  // File paths for Engine and related files
  const enginePath = join(constants.SOLARTWEAKS_DIR, constants.ENGINE.ENGINE);
  const configExamplePath = join(
    constants.SOLARTWEAKS_DIR,
    constants.ENGINE.CONFIG_EXAMPLE
  );
  const metadataPath = join(
    constants.SOLARTWEAKS_DIR,
    constants.ENGINE.METADATA
  );
  const configPath = join(constants.SOLARTWEAKS_DIR, constants.ENGINE.CONFIG);

  // Check if the Engine configuration file exists; create it if not.
  logger.debug('Verifying Config Exists...');
  await stat(configPath).catch(
    async () => await writeFile(configPath, '{}', 'utf-8')
  );

  // Fetch the updater index for the latest Engine version.
  logger.debug('Fetching Updater Index');
  const release = await axios.get(`${constants.API_URL}${constants.UPDATERS.INDEX}`).catch((reason) => {
    logger.throw('Failed to Fetch Updater Index', reason);
  });

  // Fetch and save the Config Example and Metadata files.
  await Promise.all([
    // Fetch Config Example
    await axios.get(
      `${constants.API_URL}${constants.UPDATERS.CONFIG_EXAMPLE.replace(
        '{version}',
        release?.data?.index?.stable?.config ?? 'example'
      )}`
    ).then((res) => {
      // Save Config Example
      logger.debug(`Fetched Config Example:`, res.data);
      if (res.status == 200)
        return writeFile(configExamplePath, JSON.stringify(res.data), 'utf-8');
    }).catch((err) => logger.throw('Failed to Fetch Config Example:', err)),

    // Fetch Metadata
    await axios.get(
      `${constants.API_URL}${constants.UPDATERS.METADATA.replace(
        '{version}',
        release?.data?.index?.stable?.metadata ?? '1.0.0'
      )}`
    ).then((res) => {
      // Save Metadata
      logger.debug(`Fetched Metadata:`, res.data);
      if (res.status == 200)
        return writeFile(metadataPath, JSON.stringify(res.data), 'utf-8');
    }).catch((err) => logger.throw('Failed to Fetch Metadata:', err)),
  ]);

  // Check if the release data exists.
  if (!release) return;

  // Determine the newest Engine version and the current version.
  const newest = release.data.index.stable.engine;
  const current = await settings.get('engineVersion');

  // Check if the Engine file needs to be downloaded.
  if (
    !current || // Not installed (first usage)
    !(await stat(enginePath).catch(() => false)) || // Engine doesn't exist, download
    newest !== current // Out of Date
  ) {
    // Download the Engine.
    logger.info(`Downloading Engine v${newest}, current is ${current ? `v${current}` : '"not installed"}...`);
    await downloadAndSaveFile(
      `${constants.API_URL}${constants.UPDATERS.ENGINE.replace('{version}', newest)}`,
      enginePath,
      'blob'
    ).catch((err) => logger.throw('Failed to download Engine', err));

    // Update the configuration file.
    if (current && newest) {
      if (!current.toString().startsWith(newest.toString().charAt(0)))
        await writeFile(configPath, '{}', 'utf-8');
    } else if (!current) await writeFile(configPath, '{}', 'utf-8');

    // Set the new Engine version in settings.
    await settings.set('engineVersion', release.data.index.stable.engine);
  }
}
```

### Logging

The code uses a custom logging mechanism through the `Logger` class for informative messages.

```javascript
// Example of creating a logger instance.
const logger = new Logger('Engine');
```

## Usage

To use the `verifyEngine` function:

1. Import the function from `engine.js`.
2. Call `verifyEngine()` to ensure the Engine and related files are up to date.

Example:

```javascript
import { verifyEngine } from './engine.js';

// Usage example
await verifyEngine();
```


## Links

- [**`engine.js`**](https://github.com/AlmostGen/Solar-Tweaks-Reborn/blob/main/src/engine.js)
