{
  title: "Platforms Configurator",
  alternate_title: "Automated Test Configurator",
  description: "Platforms Configurator",
  category: "Reference",
  index: 9,
  libraries: ["vendor/angular/angular.js",
  "vendor/angular-route/angular-route.min.js",
  "vendor/angular-animate/angular-animate.min.js"
  ],
  scripts: [
  "scripts/docs/platforms-configurator/app.js",
  "scripts/docs/platforms-configurator/docsConfigurator/services.js",
  "scripts/docs/platforms-configurator/docsConfigurator/constants.js",
  "scripts/docs/platforms-configurator/docsConfigurator/controllers.js",
  "scripts/docs/platforms-configurator/docsConfigurator/directives.js",
  "scripts/docs/platforms-configurator/docsConfigurator/filters.js"
  ],
  styles: [
  "styles/docs/platforms-configurator.css"
  ]
}
<base href="/configurator/platforms-configurator">

<link href='https://cdnjs.cloudflare.com/ajax/libs/highlight.js/8.2/styles/monokai_sublime.min.css' rel='stylesheet' type='text/css'>
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/8.2/highlight.min.js"></script>

<div class="platforms-configurator row" ng-app="docsConfigurator">
  <ng-view></ng-view>
</div>