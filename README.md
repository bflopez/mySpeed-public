# mySpeed-public

### www/js/app.js

```
angular.module('starter.controllers', [])

  .controller('AppCtrl', function ($scope, $ionicModal, $timeout) {

    // With the new view caching in Ionic, Controllers are only called
    // when they are recreated or on app start, instead of every page change.
    // To listen for when this page is active (for example, to refresh data),
    // listen for the $ionicView.enter event:
    //$scope.$on('$ionicView.enter', function(e) {
    //});
    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {

    }

  })


  .controller('settingsCtrl', function ($scope) {
    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
      AppRate.preferences.storeAppURL.android = 'market://details?id=com.bryanlopez.mySpeed';
    }

    $scope.sendFeedback = function () {
      if (window.plugins && window.plugins.emailComposer) {
        window.plugins.emailComposer.showEmailComposerWithCallback(function (result) {
            console.log("Response -> " + result);
          },
          "Feedback for mySpeed", // Subject
          "",                      // Body
          ["hello@bryanflopez.com"],    // To
          null,                    // CC
          null,                    // BCC
          false,                   // isHTML
          null,                    // Attachments
          null);                   // Attachment Data
      }
    };

    $scope.rateApp = function () {
      AppRate.navigateToAppStore();
    }

  })
  .controller('homeCtrl', function ($scope, $ionicPlatform, $timeout) {

    $scope.$on('$ionicView.loaded', function() {
      $ionicPlatform.ready( function() {
        if(navigator && navigator.splashscreen) navigator.splashscreen.hide();
      });
    });

    $timeout(function(){
      $scope.unit = 'mph';
      $scope.speed = 0;
      $scope.topSpeed = 0;
    });

    function onSuccess(position) {
      if (position.coords.speed < 0) {
        position.coords.speed = 0;
      }
      switch ($scope.unit){
        case 'km/h':
          $timeout(function(){
            $scope.speed = metersToKilometers(position.coords.speed);
            if($scope.speed > $scope.topSpeed){
              $scope.topSpeed = $scope.speed;
            }
          });
              break;
        case 'mph':
          $timeout(function(){
            $scope.speed = metersToMiles(position.coords.speed);
            if($scope.speed > $scope.topSpeed){
              $scope.topSpeed = $scope.speed;
            }
          });
              break;
        case 'knots':
          $timeout(function(){
            $scope.speed = metersToKnots(position.coords.speed);
            if($scope.speed > $scope.topSpeed){
              $scope.topSpeed = $scope.speed;
            }
          });
      }
    }

    function onError(error) {
      alert('code: ' + error.code + '\n' +
        'message: ' + error.message + '\n');
    }
    function round(value, decimals) {
      return Number(Math.round(value + 'e' + decimals) + 'e-' + decimals);
    }

    function metersToMiles(mps) { //meters per second to MPH
      return round(mps * 2.2369362920544, 0);
    }
    function metersToKilometers(mps) {
      var mph = metersToMiles(mps);
      return round(mph * 1.60934, 0);
    }
    function metersToKnots(mps){
      return round(mps / .51, 0)
    }
    function milesToKilometers(mph) {
      return round(mph * 1.60934, 0);
    }
    function milesToKnots(miles) {
      return round(miles / 1.15078, 0)
    }
    function kilometersToMiles(kph) {
      return round(kph / 1.60934, 0);
    }
    function kilometersToKnots(kph){
      return round(kph / 1.852, 0)
    }
    function knotsToMiles(knots){
      return round(knots * 1.15078, 0)
    }
    function knotsToKilometers(knots){
      return round(knots * 1.852, 0)
    }



    $scope.kph = function(){
      if($scope.unit === 'mph'){
        $timeout(function(){
          $scope.unit = 'km/h';
          $scope.speed = milesToKilometers($scope.speed);
          $scope.topSpeed = milesToKilometers($scope.topSpeed);
        })
      }else{
        $timeout(function(){
          $scope.unit = 'km/h';
          $scope.speed = knotsToKilometers($scope.speed);
          $scope.topSpeed = knotsToKilometers($scope.topSpeed);
        })
      }
    };
    $scope.mph = function(){
      if ($scope.unit === 'km/h'){
        $timeout(function(){
          $scope.unit = 'mph';
          $scope.speed = kilometersToMiles($scope.speed);
          $scope.topSpeed = kilometersToMiles($scope.topSpeed);
        })
      }else{
        $timeout(function(){
          $scope.unit = 'mph';
          $scope.speed = knotsToMiles($scope.speed);
          $scope.topSpeed = knotsToMiles($scope.topSpeed);
        })
      }
    };
    $scope.knots = function(){
      if ($scope.unit === 'km/h'){
        $timeout(function(){
          $scope.unit = 'kn';
          $scope.speed = kilometersToKnots($scope.speed);
          $scope.topSpeed = kilometersToKnots($scope.topSpeed);
        })
      }else{
        $timeout(function(){
          $scope.unit = 'kn';
          $scope.speed = milesToKnots($scope.speed);
          $scope.topSpeed = milesToKnots($scope.topSpeed);
        })
      }
    };

    $scope.reset = function(){
      $timeout(function(){
        $scope.topSpeed = 0;
      })
    };

    document.addEventListener("deviceready", onDeviceReady, false);
    window.addEventListener("orientationchange", function(){
      console.log(screen.orientation); // e.g. portrait
    });
    function onDeviceReady() {
      $timeout(function() {
        // $interval($scope.getCurrentSpeed(), 5000);
        $scope.watchId = navigator.geolocation.watchPosition(onSuccess, onError, {
          maximumAge: 1000,
          enableHighAccuracy: true
        });
        screen.unlockOrientation();
      });
    }

    $scope.$on('$ionicView.loaded', function () {
      $ionicPlatform.ready(function () {
        if (navigator && navigator.splashscreen) navigator.splashscreen.hide();
      });
    });

    $scope.$on('$ionicView.beforeLeave', function() {
      $timeout(function() {
        navigator.geolocation.clearWatch($scope.watchId);
      });
    });

    $ionicPlatform.on('resume', function() {
      $timeout(function() {
        navigator.geolocation.clearWatch($scope.watchId);
        $scope.watchId = navigator.geolocation.watchPosition(onSuccess, onError, {
          maximumAge: 1000,
          enableHighAccuracy: true
        });
      });
    });

  });
```

### /www/js/controller.js

```
angular.module('starter.controllers', [])

  .controller('AppCtrl', function ($scope, $ionicModal, $timeout) {

    // With the new view caching in Ionic, Controllers are only called
    // when they are recreated or on app start, instead of every page change.
    // To listen for when this page is active (for example, to refresh data),
    // listen for the $ionicView.enter event:
    //$scope.$on('$ionicView.enter', function(e) {
    //});
    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {

    }

  })


  .controller('settingsCtrl', function ($scope) {
    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
      AppRate.preferences.storeAppURL.android = 'market://details?id=com.bryanlopez.mySpeed';
    }

    $scope.sendFeedback = function () {
      if (window.plugins && window.plugins.emailComposer) {
        window.plugins.emailComposer.showEmailComposerWithCallback(function (result) {
            console.log("Response -> " + result);
          },
          "Feedback for mySpeed", // Subject
          "",                      // Body
          ["hello@bryanflopez.com"],    // To
          null,                    // CC
          null,                    // BCC
          false,                   // isHTML
          null,                    // Attachments
          null);                   // Attachment Data
      }
    };

    $scope.rateApp = function () {
      AppRate.navigateToAppStore();
    }

  })
  .controller('homeCtrl', function ($scope, $ionicPlatform, $timeout) {

    $scope.$on('$ionicView.loaded', function() {
      $ionicPlatform.ready( function() {
        if(navigator && navigator.splashscreen) navigator.splashscreen.hide();
      });
    });

    $timeout(function(){
      $scope.unit = 'mph';
      $scope.speed = 0;
      $scope.topSpeed = 0;
    });

    function onSuccess(position) {
      if (position.coords.speed < 0) {
        position.coords.speed = 0;
      }
      switch ($scope.unit){
        case 'km/h':
          $timeout(function(){
            $scope.speed = metersToKilometers(position.coords.speed);
            if($scope.speed > $scope.topSpeed){
              $scope.topSpeed = $scope.speed;
            }
          });
              break;
        case 'mph':
          $timeout(function(){
            $scope.speed = metersToMiles(position.coords.speed);
            if($scope.speed > $scope.topSpeed){
              $scope.topSpeed = $scope.speed;
            }
          });
              break;
        case 'knots':
          $timeout(function(){
            $scope.speed = metersToKnots(position.coords.speed);
            if($scope.speed > $scope.topSpeed){
              $scope.topSpeed = $scope.speed;
            }
          });
      }
    }

    function onError(error) {
      alert('code: ' + error.code + '\n' +
        'message: ' + error.message + '\n');
    }
    function round(value, decimals) {
      return Number(Math.round(value + 'e' + decimals) + 'e-' + decimals);
    }

    function metersToMiles(mps) { //meters per second to MPH
      return round(mps * 2.2369362920544, 0);
    }
    function metersToKilometers(mps) {
      var mph = metersToMiles(mps);
      return round(mph * 1.60934, 0);
    }
    function metersToKnots(mps){
      return round(mps / .51, 0)
    }
    function milesToKilometers(mph) {
      return round(mph * 1.60934, 0);
    }
    function milesToKnots(miles) {
      return round(miles / 1.15078, 0)
    }
    function kilometersToMiles(kph) {
      return round(kph / 1.60934, 0);
    }
    function kilometersToKnots(kph){
      return round(kph / 1.852, 0)
    }
    function knotsToMiles(knots){
      return round(knots * 1.15078, 0)
    }
    function knotsToKilometers(knots){
      return round(knots * 1.852, 0)
    }



    $scope.kph = function(){
      if($scope.unit === 'mph'){
        $timeout(function(){
          $scope.unit = 'km/h';
          $scope.speed = milesToKilometers($scope.speed);
          $scope.topSpeed = milesToKilometers($scope.topSpeed);
        })
      }else{
        $timeout(function(){
          $scope.unit = 'km/h';
          $scope.speed = knotsToKilometers($scope.speed);
          $scope.topSpeed = knotsToKilometers($scope.topSpeed);
        })
      }
    };
    $scope.mph = function(){
      if ($scope.unit === 'km/h'){
        $timeout(function(){
          $scope.unit = 'mph';
          $scope.speed = kilometersToMiles($scope.speed);
          $scope.topSpeed = kilometersToMiles($scope.topSpeed);
        })
      }else{
        $timeout(function(){
          $scope.unit = 'mph';
          $scope.speed = knotsToMiles($scope.speed);
          $scope.topSpeed = knotsToMiles($scope.topSpeed);
        })
      }
    };
    $scope.knots = function(){
      if ($scope.unit === 'km/h'){
        $timeout(function(){
          $scope.unit = 'kn';
          $scope.speed = kilometersToKnots($scope.speed);
          $scope.topSpeed = kilometersToKnots($scope.topSpeed);
        })
      }else{
        $timeout(function(){
          $scope.unit = 'kn';
          $scope.speed = milesToKnots($scope.speed);
          $scope.topSpeed = milesToKnots($scope.topSpeed);
        })
      }
    };

    $scope.reset = function(){
      $timeout(function(){
        $scope.topSpeed = 0;
      })
    };

    document.addEventListener("deviceready", onDeviceReady, false);
    window.addEventListener("orientationchange", function(){
      console.log(screen.orientation); // e.g. portrait
    });
    function onDeviceReady() {
      $timeout(function() {
        // $interval($scope.getCurrentSpeed(), 5000);
        $scope.watchId = navigator.geolocation.watchPosition(onSuccess, onError, {
          maximumAge: 1000,
          enableHighAccuracy: true
        });
        screen.unlockOrientation();
      });
    }

    $scope.$on('$ionicView.loaded', function () {
      $ionicPlatform.ready(function () {
        if (navigator && navigator.splashscreen) navigator.splashscreen.hide();
      });
    });

    $scope.$on('$ionicView.beforeLeave', function() {
      $timeout(function() {
        navigator.geolocation.clearWatch($scope.watchId);
      });
    });

    $ionicPlatform.on('resume', function() {
      $timeout(function() {
        navigator.geolocation.clearWatch($scope.watchId);
        $scope.watchId = navigator.geolocation.watchPosition(onSuccess, onError, {
          maximumAge: 1000,
          enableHighAccuracy: true
        });
      });
    });

  });
```
