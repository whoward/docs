{
  title: "Platforms Configurator",
  alternate_title: "Automated Test Configurator",
  description: "Platforms Configurator",
  category: "Reference",
  index: 9,
  libraries: ["vendor/angular/lib/angular.js",
  "vendor/angular-route/angular-route.min.js",
  "vendor/angular-animate/angular-animate.min.js"
  ],
  scripts: [
  "scripts/docs/platforms-configurator/highlight.pack.js",
  "scripts/docs/platforms-configurator/app.js",
  "scripts/docs/platforms-configurator/myApp/services.js",
  "scripts/docs/platforms-configurator/myApp/constants.js",
  "scripts/docs/platforms-configurator/myApp/controllers.js",
  "scripts/docs/platforms-configurator/myApp/directives.js",
  "scripts/docs/platforms-configurator/myApp/filters.js"
  ],
  styles: [
  "styles/docs/platforms-configurator.css",
  "styles/pages/monokai_sublime.css"
  ]
}

<div class="platforms-configurator row" ng-app="myApp">
  <div ng-view></div>
</div>