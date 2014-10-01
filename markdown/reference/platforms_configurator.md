{
  title: "Platforms Configurator",
  description: "Platforms Configurator",
  category: "Reference",
  index: 9
}

<script>
  window.restBaseUrl = '!{data.restBaseUrl}'
</script>

<link href="/styles/pages/configurator.css" rel='stylesheet' type='text/css' />
<link href="/styles/pages/monokai_sublime.css" rel='stylesheet' type='text/css' />

<script src="/scripts/configurator/highlight.pack.js"></script>
<script src="/scripts/configurator/app.js"></script>
<script src="/scripts/configurator/myApp/services.js"></script>
<script src="/scripts/configurator/myApp/controllers.js"></script>
<script src="/scripts/configurator/myApp/directives.js"></script>
<script src="/scripts/configurator/myApp/filters.js"></script>
<script src="/scripts/configurator/myApp/animations.js"></script>

<div class="platforms-configurator row" ng-app="myApp">
  <div ng-view></div>
</div>