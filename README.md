# Hass.io Add-on: Dropbox Sync
Back up your Hass.io snapshots to Dropbox.

### About
This add-on allows you to upload your Hass.io snapshots to your Dropbox, keeping your snapshots safe and available in case of hardware failure. Uploads are triggered via a service call, making it easy to automate periodic backups or trigger uploads to Dropbox via script as you would with any other Home Assistant service.

This add-on uses the [Dropbox-Uploader](https://github.com/andreafabrizi/Dropbox-Uploader) bash script to upload files to Dropbox. It requires that you generate an access token via the Dropbox Web UI, which must be added to this add-on's configuration via the Hass.io UI (see below for further details).

### Installation
1. Add the add-ons repository to your Hass.io instance: `https://github.com/danielwelch/hassio-addons`
2. Install the Dropbox Sync add-on
3. Configure the add-on with your Dropbox OAuth Token and desired output directory (see configuration below)

### Usage

Dropbox Sync uploads all snapshot files (specifically, all `.tar` files) in the Hass.io `/backup` directory to a specified path in your Dropbox. This target path is specified via the `output` option. Once the add-on is started, it is listening for service calls.

After the add-on is configured and started, trigger an upload by calling the `hassio.addon_stdin` service with the following service data:

`{"addon":"local_dropbox_sync","input":{"command":"upload"}}`

This triggers the `dropbox_uploader.sh` script with the provided access token. You can use Home Assistant automations or scripts to run uploads at certain time intervals, under certain conditions, etc.

Note that Dropbox Sync will only upload new snapshots to the specified path, and will skip snapshots already in the target Dropbox path.

### Configuration

To access your personal Dropobox, this add-on (and the `Dropbox-Uploader` script more generally) requires an access token. Follow these steps to create an Access Token:
1. Go to `https://www.dropbox.com/developers/apps`
2. Click the "Create App" button
3. Follow the prompts to set permissions and choose a unique name for your "app" token.

Once you have created the token, copy it into this add-on's configuration under the `oauth_access_token` label. 

|Parameter|Required|Description|
|---------|--------|-----------|
|`oauth_access_token`|Yes|The "app" access token you generated above via the Dropbox UI.|
|`output`|Yes|The target directory in your Dropbox to which you want to upload. If left empty, defaults to `/`, which represents the top level of directory of your Dropbox.|

Example Configuration:
```json
{
  "oauth_access_token": "<YOUR_TOKEN>",
  "output": "/hasssio-backups/"
}
```

### Suggestions and Issues
If you have suggestions or use-cases not covered by this add-on, please leave a comment on [the forum topic](https://community.home-assistant.io/t/hass-io-add-on-upload-hassio-snapshots-to-dropbox/). Otherwise, you may file an issue here. The flexibility of the service call and JSON service data means that this add-on could be expanded to include new features or options relatively easily.
