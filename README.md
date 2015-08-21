# treehouse_angular_from_solutions
Refactoring the final project solutions

http://teamtreehouse.com/library/angularjs

Dependencies:

AngularJS v1.2.25,
jQuery JavaScript Library v1.11.0,
Underscore.js 1.7.0

Review:

A found that watching this course at 75% was the only way to understand the material. The tutorial is not for beginners. And even after watching the tutorial I had to review the solution files several times before understanding how things were working.

I would highly recommend taking the effort to go through this tutorial and trying to code the app from first watching the video alone and then going back and reviewing the solutions that weren't explained in the video. I initially came up with a solution that worked but was clumsy. This clumsy solution did allow me to understand scopes better. The instructor solutions showed me the Angular way to structure this app.

Take Aways:

1. debug display info, a separate controller just to display this.
2. how the instrutor works out the view but spends no time trying to make it pretty, just functional. Focus on code.
3. how each controller gets the User so that it has a local reference to a persistent data structure, the user object
4. how the data is automatically inserted into the object/hash data stucture via the ng-model reference.

Notes:

I made some annotations inside app.js (and below) explaining some things about the code that were not obvious to me. I also refactored some of the code to see if some code was truly necessary. 

Since the barChart directive below uses an external file, Chrome was giving me and error. See following link.

http://stackoverflow.com/questions/27742070/angularjs-error-cross-origin-requests-are-only-supported-for-protocol-schemes

So I took the advice and ran node/npm with the http-server to serve the application. I did find that I had to have Chrome go to http://localhost:8080 instead of http://0.0.0.0:8080 per the http-server instructions.

Starting the server from a command prompt is:
> http-server C:\location\to\app

  .directive('barChart', function () {
    return {
      templateUrl: 'barChart.html',

*******************************************************************************************************

At first I modified the input to the $watch function and later removed both watchers as I could not see a reason that they were needed since the objects being watched were never changing in the specific controller. The use of the watcher may be a preferred usage pattern or it may be insurance against future edits to the code that potentially could change the user object.

    // DRT - this must be called at least once. not sure why there was a watch surrounding this since
    // questions does not change, and surveyAnswers changing doesnt require new questions
    $scope.questionsForUser = filterQuestions();

// Looks like the following code has no effect on the code. This code could be included if there was
// something the user could change that would require the questionsForUser to change. Maybe this is a
// just in case safe-guard for future proofing the program. ie. eliminate the possibility of bugs.
    // at this point the only thing that can change on user is user.surveyAnswers
    // since surveyAnswers is now modified in this controller.
    // however, will this watch be triggered when user is changed before the watch
    // is invoked. can it be aware of that?
    // let's change the watch from user to user.surveyAnswers and add a console.log
    // to see when the watch is first invoked.
//    $scope.$watch('user', function () {
 /*   $scope.$watch('user.surveyAnswers', function () {
      $scope.questionsForUser = filterQuestions();
      console.log("first watch triggered")
      console.log(JSON.stringify($scope.user))
    }, true);*/

/*
    $scope.$watch('survey.questions', function () {
      $scope.questionsForUser = filterQuestions();
      console.log("second watch triggered")
      console.log(JSON.stringify($scope.survey.questions))
    }, true);
    console.log("Both $watches have been setup inside surveyStepCtrl")
*/
    
    // changed function declaration so that hoisting will happen and I can reference filterQuestions above before defining it
//    var filterQuestions = function () {
    function filterQuestions() {
      if (!$scope.user || !$scope.survey || !$scope.survey.questions) {
        return;
      };

************************************************************************************************

What is interesting about the following code is that the Survey data structure which contains the questions and possible answers also contains javascript source code that is meant to be evaluated by the app to determine if conditional questions should be displayed.

// what's not obvious about the code below is as follows, either "conditional" will be false (passing the first test),
// which means that the question should always be displayed and therefore pushed onto the filtered list
// or else "conditional" will be an expression of the form  "user.ageRange == '40-49'". This expression will be passed
// to the $eval function which will determine if the expression is true, in which case it will be included in the
// filtered list. This mechanism allows for conditional questions depending on some criteria about the user.
if (!question.conditional || $scope.$eval(question.conditional)) {