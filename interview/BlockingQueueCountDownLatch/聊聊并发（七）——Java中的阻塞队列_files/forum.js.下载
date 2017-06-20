/*globals $,ReplyBoxHandler,OverlayHandler,EditCommentBoxHandler*/
/* takes care of the hovers for the tree comments, leave a comment link, Reply links in the flat comments, tree
 * and flat comments contents */
var CommentsHandler = {

    referenceCommentId : -1, // the id of the comment that is about to become a parent of the new comment
    subjectTouched: false,
    bodyTouched: false,

    activate : function () {
        'use strict';
        // take care of the relative times in the flat comments as well as the tree comments
        this.populateAgoTimes();
        this.activateReplyLinks();
        this.activateEditLinks();
        this.activateTreeCommentsHover();
        this.activateRootPost();
    },

    showNotificationInput : function () {
        if (CommentsHandler.bodyTouched
                && CommentsHandler.subjectTouched
                && $.trim($('#subject').val()).length !== 0
                && $.trim($('#body').val()).length !== 0) {
            $('.comment_here input').attr('checked', forumNotificationDefault);
            $('.comment_here input[name=emailMe], .comment_here span.tocheck').fadeIn(3000);
        }
    },

    populateAgoTimes : function () {
        'use strict';
        var that = this;
        $('div[id^=ans]').each(function () {
            that.populateAgoTime(this);
            // obtain the corresponding timestamp
        });
    },

    populateAgoTime : function (flatCommentNode) {
        'use strict';
        var commentId,
            agoTime = this.getRelativeTime($('input[name=tmstmp]', flatCommentNode).attr('value'));
        // populate the flat comment relative time
        $('span.bodyRelativeTime', flatCommentNode).text(agoTime);
        // populate the tree comment relative time
        commentId = $(flatCommentNode).attr('id').substring(3);
        commentId = parseInt(commentId);
        $('div#com' + commentId + ' span.treeRelativeTime').text(agoTime);
    },

    activateReplyLinks : function () {
        'use strict';
        var that = this;
        $('a.reply_flat').click(function () {
            that.referenceCommentId = $(this).parents('.answers').first().attr('id').substring(3);
            that.referenceCommentBody = "";
            if($(this).parents('.answers').first().children(".flat_comment_body").length) {
            	that.referenceCommentBody = $(this).parents('.answers').first().children(".flat_comment_body").html();
            }
            that.activateReplyLink($(this).parents('.answers')[0]);
            OverlayHandler.hideCommentOverlay();
        });
    },

    activateReplyLink : function (anchor, postProcCallback) {
        'use strict';
        ReplyBoxHandler.replyBoxAnchor = anchor;
        if (!loggedIn) {
            UserActions_Login.showLoginWidgetRightSide(anchor, 'commentsLogin');
        } else {
            ReplyBoxHandler.showReplyBox(postProcCallback);
        }
    },

    activateRootPost : function () {
        $('.comment_here')
                .hover(function () {
                    if (!loggedIn) {
                        $('#postFormDeck').css('display', 'block');
                    }
                },
                function () {
                    $('#postFormDeck').hide();
                });
        $('#subject').val(JSi18n.enter_subject);
        $('#body').val(JSi18n.enter_message);
        $('#subButton').click(function () {
            if (!loggedIn) {
                UserActions_Login.showLoginWidget(this, 'rootCommentLogin');
            } else {
                CommentsPoster.postRootComment();
            }
        });

        $('#subject,#body').keyup(CommentsHandler.showNotificationInput);
        $('#subject').
                focusin(function() {
                    if (!loggedIn) {
                        $('#postFormDeck').css('display', 'block');
                        return;
                    }
                    if (CommentsHandler.subjectTouched) {
                        return;
                    }
                    CommentsHandler.subjectTouched = true;
                    $(this).val('');
                    $(this).css('color', 'black');
                    $(this).css('font-family', 'Verdana,Geneva,sans-serif');
                    $(this).css('font-size', '13px');
                    $(this).css('font-weight', 'normal');
        });
        $('#body')
                .focusin(function() {
                    if (!loggedIn) {
                        $('#postFormDeck').css('display', 'block');
                        return;
                    }
                    if (CommentsHandler.bodyTouched) {
                        return;
                    } 
                    CommentsHandler.bodyTouched = true;
                    $(this).val('');
                    $(this).css('color', 'black');
                    $(this).css('font-family', 'Verdana,Geneva,sans-serif');
                    $(this).css('font-size', '13px');
                    $(this).css('font-weight', 'normal');
                    $('#body').autosize(); // call this after the font is updated, it only reads font related style properties once
                }).focusin( function() {
                    $('.comment_here p.allowed').fadeIn(3000);
                });
        $('#body').focusout(function() {
            $('.comment_here p.allowed').fadeOut(300);
        });
        $('#postFormDeck a.login').click(function () {
            $('#postFormDeck').css('display', 'block');
            $('div.comment_here').unbind('hover');
            UserActions_Login.showLoginWidget(this, 'postFormDeck');
        });
    },

    deactivateReplyLinks : function () {
        'use strict';
        $('a.reply_flat').unbind('click');
    },

    activateEditLinks : function () {
        'use strict';
        $('div[id^=ans]').each(function () {
            CommentsHandler.activateEditLink(this);
        });
    },

    activateEditLink : function (flatComment) {
        'use strict';
        var that = this,
            editLink = $('ul.help_links>li>a.edit_comment', flatComment)[0];
        // do nothing if the edit link is not available
        if (!editLink) {
            return;
        }
        that.updateCountdownLink(editLink);
        if (editLink) { //might have been deleted as part of the previous method call
            $(editLink).click(function () {
                // the tree comment will need the reference id
                that.referenceCommentId = $(this).parents('.answers').first().attr('id').substring(3);
                EditCommentBoxHandler.clearEditCommentBox();
                EditCommentBoxHandler.prepopulateSubjectAndBody();
                EditCommentBoxHandler.showEditCommentBox($(this).parents('.answers').first());
            });
        }
    },

    updateCountdownLink : function (linkNode) {
        'use strict';
        var parent = $(linkNode).parents('.answers')[0],
            msDelta = new Date().getTime() - parseInt($('input[name=tmstmp]', parent).val()),
            minutesRemaining = commentEditPeriod - Math.floor(msDelta / (1000 * 60));
        if (minutesRemaining <= 0) {
            // remove the edit link from the node
            $(linkNode).parent().remove();
        } else {
            if (minutesRemaining === 1) {
                minutesRemaining = '< 1';
            }
            $(linkNode).text(JSi18n.editLinkFormat.replace(/\$m/, minutesRemaining));
            setTimeout(function () {
                CommentsHandler.updateCountdownLink(linkNode);
            }, 10 * 1000); // 10 seconds error allowed
        }
    },

    deactivateEditLinks : function () {
        'use strict';
        $('ul.help_links>li>a.edit_comment').off('click');
    },

    activateTreeCommentsHover : function () {
        'use strict';
        // register the hovers for all the tree comment elements
        $('div[id^=com]').hoverIntent({
            sensitivity: 1,
            interval: 150,
            over: function () {
                //unfortunately the id of the text box is also starting with 'com' so we check if the id is there
                if (isNaN(parseInt(this.id.substring(3)))) return;
                // extract the comment id (also known as message id)
                CommentsHandler.referenceCommentId = $(this).attr('id').substring(3);
                // if rehovered, do nothing
                if (this !== OverlayHandler.lastHovered) {
                    ReplyBoxHandler.hideReplyBox();
                    UserActions_Login.hideLoginWidget();
                    OverlayHandler.lastHovered = this;
                    OverlayHandler.showCommentOverlay();
                }
            },
            out: function () {}
        });
    },

    deactivateTreeCommentsHover : function () {
        'use strict';
        $('div[id^=com]').hoverIntent({
            over: function () {},
            out: function () {}
        });
    },

    getRelativeTime : function (timeInMilliseconds) {
        'use strict';
        // create a new date with the browser locale
        var date = new Date();
        // set the milliseconds to this localized date
        date.setTime(timeInMilliseconds);
        // get the relative time
        moment.lang(InfoQConstants.language);
        var momentDate = moment(date);
        var momentNow = moment();
       
        if(momentNow.diff(momentDate,"days")>=3){
	      	// moment.js needs upper case for date format to work as we have for content.
	      	return momentDate.format(JSi18n.content_datetime_format.toUpperCase()+" hh:mm");
        }else{
        	return momentDate.fromNow();
        }        
    },

    clearRootPostBox : function () {
        $('#subject').val('');
        $('#body').val('');
        $('.comment_here input[name=emailMe], .comment_here span.tocheck').hide();
        CommentsHandler.bodyTouched = false;
        CommentsHandler.subjectTouched = false;
    },
    
    quoteOriginalMessage : function() {
    	if(this.referenceCommentBody && this.referenceCommentBody != "") {
    		$('textarea.commentsReply').val($('textarea.commentsReply').val() + "<blockquote>" + $.trim(this.referenceCommentBody) + "</blockquote>");
    	}
    	
    }
};

$(function () {
    'use strict';
    CommentsHandler.activate();
    // activate number of comments
    $('span.author_general').append($('#noOfComments'));
    $('#noOfComments').show();
});/*globals $,ReplyBoxHandler,OverlayHandler,CommentsHandler,PageNotifier*/

/* takes care of the comments posting functionality: form validation, result processing*/
var CommentsPoster = {
    postComment : function () {
        'use strict';
        // protect against multiple submits        
        CommentsPoster.disableForumPost("submit-reply", "replyPopup"); 
        
        var replyBox = $('#replyPopup'),
            subject = $('.subject', replyBox).val(), // new comment subject
            body = Parser.fixTags($('textarea.commentsReply', replyBox).val()), // new comment body
            notification = $('.emailMe', replyBox).is(':checked'); // email me checkbox
            //keep the setting for this session in case the user unchecks the default
            forumNotificationDefault = notification;

        // make sure valid data is submitted, do nothing otherwise
        if ($.trim(subject).length === 0) {
            PageNotifier.showNotificationPopup(JSi18n.errorSubject, CommentsPoster.redrawOverlayAndReplyBox);
            this.enableForumPost("submit-reply", "replyPopup");
            return;
        }
        if ($.trim(body).length === 0) {
			PageNotifier.showNotificationPopup(JSi18n.errorBody, CommentsPoster.redrawOverlayAndReplyBox);
			CommentsPoster.enableForumPost("submit-reply", "replyPopup");
			return;
        }
        // NOTE: the callback with params is possible becaus ethe function beeing called here returns another function that does the desired effect
        UserActions_Profile.forceUpdateProfile(CommentsPoster.sendRequest(postAddress, subject, body, notification, CommentsHandler.referenceCommentId, CommentsPoster.handleResponse));        
    },

    postRootComment : function () {
    	// protect against multiple submits
    	CommentsPoster.disableForumPost("subButton", "comment_here");
    	
        var notification = $('#emailMe').is(':checked'),
            subject = $('#subject').val(),
            body = $('#body').val();

        // validation
        if (!CommentsHandler.subjectTouched || $.trim(subject).length === 0) {
            PageNotifier.showNotificationPopup(JSi18n.errorSubject);
            CommentsPoster.enableForumPost("subButton", "comment_here");
            return;
        }
        if (!CommentsHandler.bodyTouched || $.trim(body).length === 0) {
			PageNotifier.showNotificationPopup(JSi18n.errorBody);
			CommentsPoster.enableForumPost("subButton", "comment_here");
			return;
        }
        // NOTE: the callback with params is possible becaus ethe function beeing called here returns another function that does the desired effect
        UserActions_Profile.forceUpdateProfile(CommentsPoster.sendRequest(postAddress, subject, body, notification, -1, CommentsPoster.handleResponse));        
    },

    repostComment : function () {
        'use strict';
        // protect against multiple submits
        CommentsPoster.disableForumPost("resubmit-reply", "editCommentPopup");
        
        var editCommentBox = $('#editCommentPopup'),
            subject = $('.subject', editCommentBox).val(), // comment subject
            body = Parser.fixTags($('textarea.commentsReply', editCommentBox).val()), // comment body
            notification = $('.emailMe', editCommentBox).is(':checked'); // email me checkbox

        // make sure valid data is submitted, do nothing otherwise
        if ($.trim(subject).length === 0) {
            PageNotifier.showNotificationPopup(JSi18n.errorSubject, CommentsPoster.redrawEditBox);
            CommentsPoster.enableForumPost("resubmit-reply", "editCommentPopup");
            return;
        }
        if ($.trim(body).length === 0) {
			PageNotifier.showNotificationPopup(JSi18n.errorBody, CommentsPoster.redrawEditBox);
			CommentsPoster.enableForumPost("resubmit-reply", "editCommentPopup");
			return;
        }
        // NOTE: the callback with params is possible becaus ethe function beeing called here returns another function that does the desired effect
        UserActions_Profile.forceUpdateProfile(CommentsPoster.sendRequest(repostAddress, subject, body, notification, CommentsHandler.referenceCommentId, CommentsPoster.handleRepostResponse));
    },

    sendRequest : function (address, subject, body, notification, parentMessageId, responseCallback) {
        var sendAjaxRequest = function(){
	    	$.ajax({
                        url: address,
                        contentType: "application/x-www-form-urlencoded; charset=UTF-8",
                        type: 'POST',
	            data: {
	                'reply' : 'true',
	                'forumID' : forumID,
	                'threadID' : threadID,
	                'messageID' : parentMessageId,
	                'subject' : subject,
	                'notification' : notification, // email me checkbox
	                'contentUrl' : document.location.href,
                    'contentTitle': contentTitle,
	                'body' : body
	            },
	            success: responseCallback,
				error: function (jqXHR, textStatus, exception) {
                    var reportLink = ". <a href='mailto:bugs@infoq.com?subject=Error posting comment&body=Posting the following comment on " + top.location.href + " does not work.%0D%0ASubject:%0D%0A" + subject + "%0D%0ABody:%0D%0A" + body + "'>Report this issue</a>.";
	                PageNotifier.showNotificationPopup(JSi18n.error + ": " + CommentsPoster.extractErrorMessage(jqXHR.responseText) + reportLink);
	            }
			});
	    	$.colorbox.close();
        }
        return sendAjaxRequest;
    },

    redrawOverlayAndReplyBox : function () {
        'use strict';
        if (OverlayHandler.lastHovered) {
            OverlayHandler.showCommentOverlay();
        }
        ReplyBoxHandler.showReplyBox();
    },

    redrawEditBox : function () {
        EditCommentBoxHandler.showEditCommentBox(ReplyBoxHandler.replyBoxAnchor);
    },

    handleResponse : function (response) {
        'use strict';
        CommentsPoster.enableForumPost("submit-reply", "replyPopup");
        CommentsPoster.enableForumPost("subButton", "comment_here");
        
        var newFlatComment,
            newTreeComment,
            level, // indentation level
            parentComment,
            nextSiblings,
            stopSibling, // the sibling that the new node is to be inserted before
            i; // iterator

        if ($.trim(response).substring(0, 6) === 'error:') {
            if (response.indexOf('Message contains invalid links') !== -1) {
                ReplyBoxHandler.showErrorMessage(JSi18n.errorInvalidLinks);
            } else if (response.indexOf('only guest user available, no logged in business user') !== -1) {
                loggedIn = false;
                PageNotifier.showNotificationPopup('[i18lize]Your session expired. Please log in and resubmit', ReplyBoxHandler.inviteToSignIn);
            } else {
                var reportLink = " <a href='mailto:bugs@infoq.com?subject=Error posting comment&body=Posting the following comment on " + top.location.href + " does not work.%0D%0ASubject:%0D%0A" + subject + "%0D%0ABody:%0D%0A" + body + "'>Report this issue</a>.";
                PageNotifier.showNotificationPopup($.trim(response).substring(6, response.length) + reportLink);
            }
        } else {
            $('#responseContent').html(response); // put the content of the response in the page, so that the next expression works for IE8
            newFlatComment = $('div[id^=ans]', $('#responseContent'));
            // add the new comment to the flat comments area
            $('div.all_comments').append(newFlatComment);
            $('a.reply_flat', newFlatComment).click(function () {
                CommentsHandler.referenceCommentId = $(this).parents('.answers').first().attr('id').substring(3);
                CommentsHandler.referenceCommentBody = "";
                if($(this).parents('.answers').first().children(".flat_comment_body").length) {
                	CommentsHandler.referenceCommentBody = $(this).parents('.answers').first().children(".flat_comment_body").html();
                }
                CommentsHandler.activateReplyLink(newFlatComment);
            });

            // add it to the tree
            newTreeComment = $('div[id^=com]', $('#responseContent'));
            parentComment = $('#com' + CommentsHandler.referenceCommentId)[0];
            if (parentComment) {
                // The 'parent' and 'child' notions are purely tree concepts.
                // Technically all the nodes are siblings, having different indentation levels, e.g. css class lvl7.
                // When adding a child, take the parent node, look up the next technical siblings, find out the first technical sibling
                // which has the same level, or a smaller lavel. If found, put the new tree coment before that sibiling
                level = CommentsPoster.findNodeLevel(parentComment);
                nextSiblings = $(parentComment).nextAll();

                for (i = 0; i < nextSiblings.length; i++) {
                    if (CommentsPoster.findNodeLevel(nextSiblings[i]) <= level) {
                        stopSibling = nextSiblings[i];
                        break;
                    }
                }

                if (stopSibling) {
                    // put it just before the parent's next same-level sibling (if available)
                    $(stopSibling).before(newTreeComment);
                } else {
                    // parent's next same level sibling not available, put it in to the global tail
                    $('div.comments').append(newTreeComment);
                }
            } else {
                // it's a 'leave a comment' one; there's no parent, put it in the tail of the tree
                $('div.comments').append(newTreeComment);
            }
            CommentsHandler.populateAgoTime(newFlatComment);
            CommentsHandler.activateEditLink(newFlatComment);
            
            OverlayHandler.hideCommentOverlay();
            ReplyBoxHandler.hideReplyBox();
            ReplyBoxHandler.clearReplyBox();
            CommentsHandler.clearRootPostBox();

            // scroll to the new flat comment
            $(window).scrollTop($(newFlatComment).offset().top);
        }
    },

    handleRepostResponse : function (response) {
        'use strict';
        CommentsPoster.enableForumPost("resubmit-reply", "editCommentPopup");
        var newContentFlat,
            oldContentFlat = $('#ans' + CommentsHandler.referenceCommentId),
            editedTreeComment;
        if ($.trim(response).substring(0, 6) === 'error:') {
            if (response.indexOf('Message contains invalid links') !== -1) {
                EditCommentBoxHandler.showErrorMessage(JSi18n.errorInvalidLinks);
            } else if (response.indexOf('You are not allowed to edit this message.') !== -1) {
                PageNotifier.showNotificationPopup(JSi18n.timeExpiredMessage);
            }  else if (response.indexOf('only guest user available, no logged in business user') !== -1) {
                loggedIn = false;
                PageNotifier.showNotificationPopup(JSi18n.sessionExpiredMessage, ReplyBoxHandler.inviteToSignIn);
            } else {
                PageNotifier.showNotificationPopup(response.substring(6, response.length));
            }
        } else {
            // update existing flat comment
            $('#responseContent').html(response);
            newContentFlat = $('div[id^=ans]', $('#responseContent'));
            $(oldContentFlat).html($(newContentFlat).html());
            // update existing tree comment
            editedTreeComment = $('div[id^=com]', $('#responseContent'));
            $('#com' + CommentsHandler.referenceCommentId).html($(editedTreeComment).html());
            CommentsHandler.populateAgoTime(oldContentFlat);
            CommentsHandler.activateEditLink(oldContentFlat);
            // scroll to the new flat comment
            $(window).scrollTop($(oldContentFlat).offset().top);
            EditCommentBoxHandler.hideEditCommentBox();
        }
    },

    findNodeLevel : function (node) {
        'use strict';
        var classRaw = $(node).attr('class');
        classRaw = classRaw ? classRaw : '';
        var classArray = classRaw.split(/\s+/),
            level,
            i;
        for (i = 0; i < classArray.length; i++) {
            if (classArray[i].substring(0, 3) === 'lvl') {
                level = classArray[i].substring(3);
                break;
            }
        }
        return level;
    },
    
    disableForumPost:function(idButton, idPopup){    	
    	// forum post needs to be disabled to protect against multiple submit situations
    	$('#'+idButton).prop('disabled', true);
        $('#'+idPopup).css("cursor", "progress");
    },
    
    enableForumPost:function(idButton, idPopup){    	
    	// forum post was disabled to prevent multiple submits situations
    	$('#'+idButton).prop('disabled', false);
        $('#'+idPopup).css("cursor", "default");
    },
    
    extractErrorMessage:function(msg){
        var startIdx=msg.indexOf("[]")+2;
        var endIdx=msg.indexOf("[/]");
        return msg.substring(startIdx, endIdx);
    }
};/*globals $,ReplyBoxHandler,CommentsHandler*/

/*takes care of the box used to reedit the contens of previously edited comments */
var EditCommentBoxHandler = {
    activate : function () {
        'use strict';
        var that = this,
            editCommentBox = $('#editCommentPopup');
        $('a.close_login_popup', editCommentBox).click(that.hideEditCommentBox);
        $('input.reset-reply', editCommentBox).click(that.hideEditCommentBox);
        // activate repost button
        $('#resubmit-reply').click(CommentsPoster.repostComment);
    },

    clearEditCommentBox : function () {
        'use strict';
        var editCommentBox = $('#editCommentPopup');
        // clear subject and body
        $('input[name=subject]', editCommentBox).val('');
        $('textarea.commentsReply', editCommentBox).val('');
        // hide invalid link error
        $('p.allowed.error', editCommentBox).css('display', 'none');
        // hide time expired error
        $('p.allowed.time_expired', editCommentBox).css('display', 'none');
        // enable the button
        $('#resubmit-reply', editCommentBox).removeAttr('disabled').removeClass('disabled');
    },

    showEditCommentBox : function (anchor) {
        'use strict';
        var that = this,
            editCommentBox = $('#editCommentPopup');
        // do nothing if the edit link is clicked again
        if (anchor === ReplyBoxHandler.replyBoxAnchor) {
            return;
        }
        ReplyBoxHandler.replyBoxAnchor = anchor;
        // referenceCommentId is the id of the comment relative to which the new comment will be inserted in the tree.
        // there is one method that tampers with the referenceCommentId - overlayHandler.showCommentOverlay
        // if the referenceCommentId is changed at this moment, the new node will be misplaced in the tree.
        // disable the hovers until the new node is placed in the tree and and activate them again when done
        CommentsHandler.deactivateTreeCommentsHover();
        CommentsHandler.deactivateReplyLinks();
        // solve Repost button text to update
        that.updateCountdownButton();
        // solve positioning
        $(editCommentBox).css('top', $(anchor).position().top
            + $(anchor).outerHeight(true));
        $(editCommentBox).css('left', $(anchor).position().left);
        $(editCommentBox).css('display', 'block');
    },

    updateCountdownButton : function () {
        'use strict';
        var myCommentId = CommentsHandler.referenceCommentId,
            creationTime = parseInt($('#ans' + CommentsHandler.referenceCommentId + ' input[name=tmstmp]').val()),
            button = $('#editCommentPopup #resubmit-reply');

        (function updateButtonText() {
            var msDelta = new Date().getTime() - creationTime,
                secondsRemaining = 60 * commentEditPeriod - Math.floor(msDelta / 1000);
            // if the reference comment id changed (another edit link was clicked, abort update
            if (myCommentId !== CommentsHandler.referenceCommentId) {
                return;
            }
            if (secondsRemaining < 0) {
                $(button).addClass('disabled').attr('disabled', 'disabled').val(JSi18n.timeExpiredButton);
                EditCommentBoxHandler.showTimeExpiredMessage(JSi18n.timeExpiredMessage);
            } else if (secondsRemaining <= 60) {
                $(button).val(JSi18n.repostButtonFormat.replace(/\$m/, secondsRemaining + 's'));
                setTimeout(updateButtonText, 1000);
            } else {
                $(button).val(JSi18n.repostButtonFormat.replace(/\$m/, Math.ceil(secondsRemaining / 60) + 'm'));
                if (secondsRemaining < 70) {
                    setTimeout(updateButtonText, 1000);
                } else {
                    setTimeout(updateButtonText, 10 * 1000);
                }
            }
        }());
    },

    hideEditCommentBox : function () {
        'use strict';
        CommentsHandler.activateTreeCommentsHover();
        CommentsHandler.activateReplyLinks();
        $('#editCommentPopup').hide();
    },

    prepopulateSubjectAndBody : function () {
        'use strict';
        var editCommentBox = $('#editCommentPopup'),
            corrFlatComment = $('#ans' + CommentsHandler.referenceCommentId),
            isNotificationEnabled = $('input[name=isNotificationOn]', corrFlatComment).val() === 'true';
        $('input[name=subject]', editCommentBox).val($('b.title', corrFlatComment).text());
        $('textarea.commentsReply', editCommentBox).val($.trim($('.flat_comment_body', corrFlatComment).html()).replace(/<br>/g, '\n'));

        $('input[name=emailMe]', editCommentBox).attr('checked', isNotificationEnabled);
    },

    showErrorMessage : function (errorMessage) {
        'use strict';
        $('p.error.allowed', $('#editCommentPopup')).css('display', 'inline').text(errorMessage);
        setTimeout(EditCommentBoxHandler.hideErrorMessage, 3000);
    },

    hideErrorMessage : function () {
        'use strict';
        $('#editCommentPopup p.error.allowed').css('display', 'none').text('');
    },

    showTimeExpiredMessage : function (errorMessage) {
        'use strict';
        $('#editCommentPopup p.time_expired.allowed').css('display', 'block').text(errorMessage);
    },

    hideTimeExpiredMessage : function () {
        'use strict';
        $('p.time_expired.allowed', $('#editCommentPopup')).css('display', 'none').text('');
    }
};

$(function () {
    'use strict';
    EditCommentBoxHandler.activate();
});/*globals $,ReplyBoxHandler,CommentsHandler./

/* takes care of the overlay UI triggered when a tree comment is hovered */
var OverlayHandler = {
    /* when the showCommentOverlay method is triggered only the overlay is shown, no Reply box or Login box.
    this method can be triggered more than once for the same element. The second time it will hide everything but
    the overlay. In order to prevent that, we check whether the hover is for that same element.*/
    lastHovered : null,

    activate : function () {
        'use strict';
        this.activateClose();
        this.activateReplyLink();
    },
    // register overlay close event
    activateClose : function () {
        'use strict';
        var that = this;
        $('#overlay_comments>a.close_comment').click(function () {
            that.hideCommentOverlay();
            ReplyBoxHandler.hideReplyBox();
        });
    },

    // associate reply links to either login or reply box
    activateReplyLink : function () {
        'use strict';
        var overlay = $('#overlay_comments');
        $('a.reply', overlay).click(function () {
            CommentsHandler.activateReplyLink(overlay);
        });
    },

    showCommentOverlay : function () {
        'use strict';

        var overlay = $('#overlay_comments'); // the overlay to be popped up

        OverlayHandler.moveData();
        OverlayHandler.configureViewLink();

        // position
        $(overlay).css('top', $(OverlayHandler.lastHovered).position().top + 'px')
            .css('left', $(OverlayHandler.lastHovered).offset().left - $('div.comments').offset().left + 200 + 'px');

        // and show
        $(overlay).css('display', 'block');
    },

    hideCommentOverlay : function () {
        'use strict';
        $('#overlay_comments').hide();
    },

    // data taken from the flat comment area and used to populate the corresponding tree comment overlay
    moveData : function () {
        'use strict';
        var corrFlatNode = $('#ans' + CommentsHandler.referenceCommentId),
            overlay = $('#overlay_comments'), // the overlay to be popped up
            title,
            author,
            body,
            timepast,
            postedOn;
        title = $('b', corrFlatNode).first().text();
        author = $('q', corrFlatNode).text();
        body = $('.flat_comment_body', corrFlatNode).html();
        timepast = $('span.bodyRelativeTime', corrFlatNode).text();
        postedOn = $('input[name=createdOn]', corrFlatNode).val();

        // populate with relevant data
        $('h4>strong', overlay).text(title);
        $('span#author', overlay).text(author);
        $('p#body', overlay).html(body);
        $('span#timepast').text(timepast);
        $('div.alt span', overlay).text(postedOn);
    },

    configureViewLink : function () {
        'use strict';
        $('#overlay_comments .view').attr('href', '#anch' + CommentsHandler.referenceCommentId);
    }
};

$(function () {
    'use strict';
    OverlayHandler.activate();
});/*globals $,ReplyBoxHandler,OverlayHandler,CommentsHandler./

/* substitute for the alert modal windows*/
var PageNotifier = {
    callbackOnClose : null,
    activate : function () {
        'use strict';
        var that = this;
        $('#messagePopup a.close_login_popup,#messagePopup .reset-reply').click(that.hideNotificationPopup);
    },

    showNotificationPopup : function (message, callbackOnClose) {
        'use strict';
        this.callbackOnClose = callbackOnClose;
        var messagePopup = $('#messagePopup'),
            scrollTop = $(window).scrollTop();
        $('p.allowed', messagePopup).html(message);
        // position
        if (ReplyBoxHandler.replyBoxAnchor) {
            $(messagePopup).css('top', $(ReplyBoxHandler.replyBoxAnchor).position().top
                + $(ReplyBoxHandler.replyBoxAnchor).outerHeight(true));
            $(messagePopup).css('left', $(ReplyBoxHandler.replyBoxAnchor).position().left);
        } else {
            $(messagePopup).css('top', $(window).scrollTop());
        }
        // and show
        $('#messagePopup').css('display', 'block');
        OverlayHandler.hideCommentOverlay();
        ReplyBoxHandler.hideReplyBox();
        EditCommentBoxHandler.hideEditCommentBox();
        CommentsHandler.deactivateTreeCommentsHover();
        CommentsHandler.deactivateReplyLinks();
        // disable page scrolling
        if (scrollTop > $(messagePopup).offset().top) {
            scrollTop = $(messagePopup).offset().top - $(window).height() / 2;
        }
        $('body,html').css('overflow', 'hidden').scrollTop(scrollTop);
    },

    hideNotificationPopup : function () {
        'use strict';
        var scrollTop = $(window).scrollTop();
        $('#messagePopup').hide();
        CommentsHandler.activateTreeCommentsHover();
        CommentsHandler.activateReplyLinks();
        CommentsPoster.enableForumPost("subButton", "comment_here");
        // re-enable page scrolling
        $('body,html').css('overflow', 'auto').scrollTop(scrollTop);
        if (PageNotifier.callbackOnClose) {
            PageNotifier.callbackOnClose.call();
        }
    }
};

$(function () {
    'use strict';
    PageNotifier.activate();
});/*when user submits html content, only some tags allowed, the rest escaped */
var Parser = {
    fixTags : function (text) {
        'use strict';
        var tagStack = [],
            output = [],
            openTag = false,
            closeTag = false,
            tagChars = [],
            length = text.length,
            tagName,
            ch = '',
            i, // iterator
            fullTagName,
            alreadyClosed,
            spaceIdx,
            lowerCaseTagName,
            lastEl,
            tag;
        for (i = 0; i < length; i++) {
            ch = text.charAt(i);
            if (((ch >= 'a') && (ch <= 'z')) || ((ch >= 'A') && (ch <= 'Z'))) {
                if (openTag || closeTag) {
                    tagChars.push(ch);
                } else {
                    output.push(ch);
                }
                continue;
            }
            if (ch === '<') {
                if (openTag || closeTag) { // flush the accumulated chars as this is possibly a tag opener
                    output.push(tagChars.join(""));
                    tagChars = [];
                }
                if (i + 2 < length && text.charAt(i + 1) !== " ") {
                    if (text.charAt(i + 1) === "/") {
                        i++;
                        if (i + 2 < length && text.charAt(i + 1) !== " ") {
                            closeTag = true;
                            tagChars.push("</");
                        } else {
                            output.push("</");
                        }
                    } else {
                        openTag = true;
                        tagChars.push("<");
                    }
                } else {
                    output.push(ch);
                }
                continue;
            }
            if (ch === '>') {
                if (openTag) {
                    // found a tag
                    fullTagName = tagChars.join("");
                    alreadyClosed = false;
                    if ("/" === text.charAt(i - 1)) {
                        // see if it is already closed
                        fullTagName = fullTagName.substring(0, fullTagName.length - 1);
                        alreadyClosed = true;
                    }
                    spaceIdx = fullTagName.indexOf(" ");
                    if (spaceIdx === -1) { // tag name only
                        tagName = fullTagName.substring(1);
                    } else { // tag with attributes
                        tagName = fullTagName.substring(1, spaceIdx);
                    }
                    lowerCaseTagName = tagName.toLowerCase();
                    if (alreadyClosed) {
                        output.push(fullTagName + "/>");
                    } else if ("br" === lowerCaseTagName) {
                        // br doesn't need a closing tag
                        output.push("<br/>");
                    } else {
                        tagStack.push(tagName.toLowerCase());
                        output.push(fullTagName + ">");
                    }
                    tagChars = [];
                    openTag = false;
                } else if (closeTag) {
                    // found close tag
                    tagName = tagChars.join("").substring(2);
                    tagChars = [];
                    closeTag = false;
                    // check if the tag is in the stack
                    if (tagStack.length > 0) {
                        lastEl = tagStack[tagStack.length - 1];
                        if (lastEl === tagName.toLowerCase()) {
                            tagStack.pop();
                        } else if ("br" !== tagName) {
                            output.push("<" + tagName + ">");
                        }
                    } else if ("br" !== tagName) {
                        output.push("<" + tagName + ">");
                    }
                    if ("br" === tagName) {
                        output.push("<br/>");
                    } else {
                        output.push("</" + tagName + ">");
                    }
                } else {
                    output.push(ch);
                }
                continue;
            }
            if (openTag) {
                tagChars.push(ch);
            } else if (closeTag) { // close tags do not allow attribute
                closeTag = false;
                output.push(tagChars.join(""));
                tagChars = [];
            } else {
                output.push(ch);
            }
        }
        if (tagChars.length > 0) {
            output.push(tagChars.join(""));
        }
        while (tagStack.length > 0) {
            tag = tagStack.pop();
            output.push("</" + tag + ">");
        }
        return output.join("");
    }
};/*global $,CommentsHandler.CommentsPoster./
/* takes care of reply box UI */
var ReplyBoxHandler = {

    replyBoxAnchor : null, // in case reply link is clicked, the reply box will be positioned relative to this element

    activate : function () {
        'use strict';
        this.activateClose();
        this.activatePostButton();
        this.activateCancelButton();
    },

    // register the reply box close event
    activateClose : function () {
        'use strict';
        var that = this;
        $('#replyPopup>a.close_login_popup').click(function () {
            that.hideReplyBox();
        });
    },

    activatePostButton : function () {
        'use strict';
        $('#submit-reply').click(CommentsPoster.postComment);
    },

    activateCancelButton : function () {
        'use strict';
        var that = this;
        $('input.reset-reply', $('#replyPopup')).click(function () {
            that.hideErrorMessage();
            that.hideReplyBox();
        });
    },

    showReplyBox : function (postProcCallback) {
        'use strict';
        var that = this,
            replyBox = $('#replyPopup');
        // referenceCommentId is the id of the comment relative to which the new comment will be inserted in the tree.
        // there is one method that tampers with the referenceCommentId - overlayHandler.showCommentOverlay
        // if the referenceCommentId is changed at this moment, the new node will be misplaced in the tree.
        // disable the hovers until the new node is placed in the tree and and activate them again when done
        CommentsHandler.deactivateTreeCommentsHover();
        CommentsHandler.deactivateEditLinks();

        // solve positioning
        $(replyBox).css('top', $(ReplyBoxHandler.replyBoxAnchor).position().top + $(ReplyBoxHandler.replyBoxAnchor).outerHeight(true));
        $(replyBox).css('left', $(ReplyBoxHandler.replyBoxAnchor).position().left);
        $(replyBox).css('display', 'block');
        // construct the subject if it's a reply. Don't if the comment originates in 'leave a comment'
        if (CommentsHandler.referenceCommentId > 0) {
            $('.subject', replyBox).val(ReplyBoxHandler.computedSubject());
            $('.subject', replyBox).select();
        }
        $('.popupLoginComments input[name=emailMe]').attr('checked', forumNotificationDefault);
        $('.commentsReply', replyBox).focus();
        
        // in case we came here from force close the popup
        $.colorbox.close();
        
        if (postProcCallback) {
            postProcCallback.call();
        }
    },

    hideReplyBox : function () {
        'use strict';
        $('#replyPopup').hide();
        // reactivate hover efect on tree comments
        CommentsHandler.activateTreeCommentsHover();
        CommentsHandler.activateEditLinks();
    },

    clearReplyBox : function () {
        'use strict';
        var replyBox = $('#replyPopup');
        $('.subject', replyBox).val('');
        $('textarea.commentsReply', replyBox).val('');
        this.hideErrorMessage();
    },

    focusOnSubject : function () {
        'use strict';
        var replyBox = $('#replyPopup');
        $('.subject', replyBox).focus();
    },

    computedSubject : function () {
        'use strict';
        var corrFlatComment = $('#ans' + CommentsHandler.referenceCommentId).first(),
            result = $('b.title', corrFlatComment).text();
        // append a Re: in front, only if it's not already starting with it
        if (result.substring(0, JSi18n.re.length) !== JSi18n.re) {
            result = JSi18n.re + ' ' + result;
        }
        // trim the subject to 75 characters
        if (result.length > 75) {
            result = result.substring(0, 75);
        }
        return result;
    },

    showErrorMessage : function (errorMessage) {
        'use strict';
        $('p.error.allowed', $('#replyPopup')).css('display', 'inline').text(errorMessage);
        setTimeout(ReplyBoxHandler.hideErrorMessage, 3000);
    },

    hideErrorMessage : function () {
        'use strict';
        $('p.error.allowed', $('#replyPopup')).css('display', 'none').text('');
    },

    inviteToSignIn: function() {
        ReplyBoxHandler.showReplyBox();
        UserActions_Login.showLoginWidgetRightSide($('#replyPopup'), 'replyBoxLogin');
    }
};

$(function () {
    'use strict';
    ReplyBoxHandler.activate();
});/*global $,PageNotifier*/

/* takes care of the watch thread/unwatch thread functionality */
var watcher = {
    activate : function () {
        'use strict';
        this.setLinkText();
        this.activateLink();
    },

    setLinkText : function () {
        'use strict';
        try{
	        if (threadWatched) {
	            $('#watch>a').text(JSi18n.stopWatchText);
	        } else {
	            $('#watch>a').text(JSi18n.startWatchText);
	        }
        }catch(err){}

    },

    watchThread : function() {
    	$.ajax({
            url: threadWatched ? postRemoveWatches : postAddWatches,
            type: 'POST',
            data: {
                'forumID': forumID,
                'threadID': threadID
            },
            complete: function (e) {
                if (e.status === 200) {
                    threadWatched = !threadWatched;
                    watcher.setLinkText();
                } else if (e.status === 401) {
                    UserActions_Login.showLoginWidget($('#watch>a'), 'watchForumThread');
                } else {
                    PageNotifier.showNotificationPopup(JSi18n.error); // reasonable fallback
                }
            }
        });
    	$.colorbox.close();
    },
    
    activateLink : function () {
        'use strict';
        $('#watch>a').click(function () {
        	UserActions_Profile.forceUpdateProfile(watcher.watchThread);
        });
    }
};

$(function () {
    'use strict';
    watcher.activate();
});