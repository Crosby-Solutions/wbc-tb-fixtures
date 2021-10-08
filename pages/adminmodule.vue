<template>
  <div class="admin">
    Admin Page
    <script src="https://identity.netlify.com/v1/netlify-identity-widget.js" />
  </div>
</template>

<script>

import CMS from 'netlify-cms-app'
import { UuidControl, UuidPreview } from 'netlify-cms-widget-uuid'
// init identify
// const netlifyIdentity = require('netlify-identity-widget');
// netlifyIdentity.init({
//     container: '#netlify-modal', // defaults to document.body
//     locale: 'en' // defaults to 'en'
// });
// Initialize the CMS object
CMS.init()
// Now the registry is available via the CMS object.
// CMS.registerPreviewTemplate('my-template', MyTemplate)
CMS.registerWidget('uuid', UuidControl, UuidPreview)
// CMS.registerWidget('uuid', NetlifyCmsWidgetUuid.UuidControl, NetlifyCmsWidgetUuid.UuidPreview);
export default {
}
</script>

<style>

</style>
