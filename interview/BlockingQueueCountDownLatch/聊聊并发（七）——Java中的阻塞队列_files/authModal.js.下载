'use strict';

var authModal = {
  init: function init() {
    var _this = this;
    var body = document.querySelector('body');

    // Keeping it one event only
    document.addEventListener('click', function (event) {
      var target = event.target;
      // Easiest mechanism
      if (target.classList.contains('authModal--trigger')) {
        body.classList.toggle('authModal--open');
        document.getElementById('auth__email').focus();
        return;
      }
      // Forgot password - switch forms
      if (target.classList.contains('auth--forgot')) {
        event.preventDefault();
        _this.switchForms();
        return;
      }
      return;
    });
  },

  get forms() {
    return document.querySelectorAll('.auth__form');
  },
  switchForms: function switchForms() {
    var _iteratorNormalCompletion = true;
    var _didIteratorError = false;
    var _iteratorError = undefined;

    try {
      for (var _iterator = this.forms[Symbol.iterator](), _step; !(_iteratorNormalCompletion = (_step = _iterator.next()).done); _iteratorNormalCompletion = true) {
        var form = _step.value;
        form.classList.toggle('form--hidden');
      }
    } catch (err) {
      _didIteratorError = true;
      _iteratorError = err;
    } finally {
      try {
        if (!_iteratorNormalCompletion && _iterator.return) {
          _iterator.return();
        }
      } finally {
        if (_didIteratorError) {
          throw _iteratorError;
        }
      }
    }
  },
  thirdPartyLogin: function (actionLink) {
        var glue = (window.location.search.indexOf('?') >= 0) ? '&' : '?';
        var origin = "&orig=" + encodeURIComponent(window.location.pathname + window.location.search + glue + 'p13nId=' + $('#p13n-id').val() + '&p13nType=' + $('#p13n-type').val());
        location.href = actionLink + '?fl=login' + origin;
        return true;
    },
  addP13NOrigin: function (link){
        var glue = (window.location.search.indexOf('?') >= 0) ? '&' : '?';
	var origin = window.location.pathname + window.location.search;
        var lglue = (link.href.indexOf('?') >= 0) ? '&' : '?';
	link.href = link.href + lglue + "orig=" + encodeURIComponent(origin  + glue + 'p13nId=' + $('#p13n-id').val()+'&p13nType=' + $('#p13n-type').val());
	return true;
  },
  showMessage: function (message) {
        $('#loginMessage').css('color', '#48A657');
        $('#loginMessage').html(message);
        $('#loginMessage').show();
  }
};

document.addEventListener('DOMContentLoaded', function () {
  authModal.init();
});
