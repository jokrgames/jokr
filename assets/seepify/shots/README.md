# Seepify screenshots

Empty on purpose. Seepify bundles no artwork at all — the cards, felt, launcher
icon and every sound are generated in code — so there is nothing in the app repo
to copy here, and no screenshot is faked on the site.

To add some:

1. Capture 1080x1920 PNGs from a device, or run the app in Chrome
   (`cd ../seepify/app && flutter run -d chrome`) and screenshot that.
2. Drop them here as `01_<name>.png`, `02_<name>.png`, …
3. Downscale them the same way the JOKR shots were:

   ```bash
   cd /Users/amitgera/amitgeraData/jokrgames/jokr
   for f in assets/seepify/shots/*.png; do
     sips -Z 960 -s format jpeg -s formatOptions 65 "$f" --out "${f%.png}.jpg"
   done
   ```

4. Delete the PNGs, then uncomment the `.shots` block in `seepify.html` and
   write real `alt` text and a caption for each one.
