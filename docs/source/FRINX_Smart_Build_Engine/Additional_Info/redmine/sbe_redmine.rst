
Redmine
=======

This is the Redmine manual for users to develop, track and manage tasks in the Secondary Build Environment. Redmine is a flexible project management Web application. Written using the Ruby on Rails framework, it is cross-platform and cross-database. Redmine is open source and released under the terms of the GNU General Public License v2 (GPL). It allows you to create multiple projects, assign multiple persons with the help of a local Wiki page and document storage place. Redmine can be used by managers, developers and reporters (support teams). It allows you to create a nice project hierarchy and to organize and track multiple bugs, features and support nice and smoothly.\ :raw-html-m2r:`<br>`
The translation of the Redmine guide is available in Japanese language `here <http://redmine.jp/guide/>`_


*  `1 Sign Up <#1-sign-up>`__
*  `2 Adding new members to your project <#2-adding-new-members-to-your-project>`__
*  `3 Creation of a project <#3-creation-of-a-project>`__
*  `4 Creation of a new issue in the project: bug, feature or support <#4-creation-of-a-new-issue-in-the-project-bug-feature-or-support>`__
*  `5 Issue management <#5-issue-management>`__
*  `6 Issues in Calendar view <#6-issues-in-calendar-view>`__
*  `7 Gantt chart <#7-gantt-chart>`__
*  `8 Sharing the news with your team <#8-sharing-the-news-with-your-team>`__
*  `9 Documentation <#9-documentation>`__
*  `10 Project’s Wiki page <#10-projects-wiki-page>`__
*  `11 Files <#11-files>`__
*  `12 Settings <#12-settings>`__
*  `13 Useful to know <#13-useful-to-know>1__

1 Sign Up
---------

Go to the Register button.

**Figure 1 Redmine registration**


.. image:: fig1.jpg
   :target: fig1.jpg
   :alt: Figure 1: Redmine registration


Complete the registration form; all necessary fields are marked with a red star.

**Figure 2 Completion of registration form**


.. image:: fig2.jpg
   :target: fig2.jpg
   :alt: Figure 2: Completion of registration form


Activate and modify your account, change the password, choose the time zone and decide how comments will be displayed.

**Figure 3 Account modification**


.. image:: fig3.jpg
   :target: fig3.jpg
   :alt: Figure 3: Account modification


Your account is ready!

2 Adding new members to your project
------------------------------------

Every new member must be signed up with his/her email address. Please read Section No. 1. When you are logged in, go to Settings and choose the Members tab.

Choose your colleague from the list and assign him/her a role of Manager, a Developer or a Reporter. Then submitt with Add button.

3 Creation of a project
-----------------------

Go to the Project tab on the top of blue screen and click on New project button.

**Figure 4 Adding a new project**


.. image:: fig4.jpg
   :target: fig4.jpg
   :alt: Figure 4: Adding a new project


Complete the project’s form according to the picture below. Use the project identifier you like, choose different types of modules/features and which type of issue you would like to track.

**Figure 5 Creation of a new project**


.. image:: fig5.jpg
   :target: fig5.jpg
   :alt: Figure 5: Creating a new project


Start 123 is an example of a project that will be shown many times in this documentation. Your project is ready to manage!

4 Creation of a new issue in the project: bug, feature or support
-----------------------------------------------------------------

It’s very easy to create a new issue in Redmine. If you are in your project area, go to the New issue tab. The Type of issue can be: bug feature support

Choose the Priority for your issue: Low Normal High Urgent Immediate Assign the task to a certain person from the Assignee drop down menu. If you don’t have anybody listed, please add your colleagues to your project and repeat Section No. 2. There is a possibility to choose Parent task, Start date, Due date as Estimated time and % Done. Don’t forget to assign Watchers. A watcher should be a person responsible for the project, a person who wants to have a complete overview about the project.

**Figure 6 Creation of issue**


.. image:: fig6.jpg
   :target: fig6.jpg
   :alt: Figure 6: Creation of issue


For a better overview, you can even link a bug to your Redmine Wiki or your corporate Wikipage using the button Link to a wiki page.

**Figure 7 Issue linking to a wiki page**


.. image:: fig7.png
   :target: fig7.png
   :alt: Figure 7: Issue linking to a wiki page


5 Issue management
------------------

All issues are listed in the Issues tab under your Project. Searching of the concrete issues or a bunch of the issues from the same category has been never easier. You can use the Add filter drop down menu on the right side and choose the filter you need. On the other hand, you have a possibility choose the concrete state of the bug in the left corner. For more details please see the picture below.

**Figure 8 List of issues**


.. image:: fig8.jpg
   :target: fig8.jpg
   :alt: Figure 8: List of issues


You can see the project’s issues in multiple ways. Reporting and logical structure can be seen by clicking on the Summary link on the left side in Issue tab. It brings you a nice overview and the current state of your bugs, features and support issues.

**Figure 9 Issue categories on the left side**


.. image:: fig9.jpg
   :target: fig9.jpg
   :alt: Figure 9: Redmine registration


**Figure 10 Summary of the bugs, features and support issues**


.. image:: fig10.jpg
   :target: fig10.jpg
   :alt: Figure 10: Redmine registration


6 Issues in Calendar view
-------------------------

The next level of information is provided by the calendar feature. By clicking on the Calendar tab under your project you can see the calendar filled with all issues that can give you a nice overview with respect to planning. Please see the picture below.

**Figure 11 Calendar view**


.. image:: fig11.jpg
   :target: fig11.jpg
   :alt: Figure 11: Redmine registration


7 Gantt chart
-------------

Visibility of all issues with regard to planning is provided in the Gantt tab under your project. The Gantt chart shows you a very detailed progress overview with the percentage of completion and the possibility to view issues from many different point of views by using filters. For more info please see the picture below.

**Figure 12 Gantt chart**


.. image:: fig12.jpg
   :target: fig12.jpg
   :alt: Figure 12: Gantt chart


8 Sharing the news with your team
---------------------------------

If you would like to share the newest information with your team and you don’t want to spam everybody, you can use News tab under your project. News will allow you to share very important information in a creative way and what is important, visible to everybody. Your colleagues can comment the article and reply to you immediately after publishing.

**Figure 13 Adding news**


.. image:: fig13.jpg
   :target: fig13.jpg
   :alt: Figure 13: Adding news


**Figure 14 List of news**


.. image:: fig14.jpg
   :target: fig14.jpg
   :alt: Figure 14: List of news


9 Documentation
---------------

Redmine allows you to create two different types of documentation. Creation of User documentation and Technical documentation is available under your project in Documents tab. After creation of documents, you can find a list of documents in logical order with the option to sort by category, date, title and author.

**Figure 15 View of Documents**


.. image:: fig15.jpg
   :target: fig15.jpg
   :alt: Figure 15: View of Documents


10 Project’s Wiki page
----------------------

Redmine allows you to create your own project’s wiki page. Wiki articles can be paired with created bugs, features or supports. More about linking to bugs can be found in Section No. 4. Output can be seen in pdf, html and txt form.

**Figure 16 Wiki**


.. image:: fig16.jpg
   :target: fig16.jpg
   :alt: Figure 16: Wiki


11 Files
--------

Share the files with your team. The Files tab under your project will allow you to share different files up to 100 MB organized by date and size.

**Figure 17 Adding multiple files**


.. image:: fig17.jpg
   :target: fig17.jpg
   :alt: Figure 17: Adding multiple files


12 Settings
-----------

You are allowed to change the design of your Redmine, set up new modules, organize the members group, use, categorize the issues and use forums for further discussions. What can be modified: **Information** The basic information of your project and type of issues (bugs, features and support) **Modules** Many different types of modules/tabs taht will be used in your project for instance Time tracking, Wiki etc **Members** Add a new member or modigy the gropups and editing the existing members **Issue categories** Create an issue category as you wish and use it for issue management **Wiki** Change the name of your wiki page **Forums** Create and manage a forum for your colleagues **Activities (time tracking)** Choose a method for how Design and Development will be tracked

13 Useful to know
-----------------

**Table 1 Useful links**\ :raw-html-m2r:`<br>`
`Redmine overview <http://www.redmine.org/>`_\ :raw-html-m2r:`<br>`
`List of features <http://www.redmine.org/>`_\ :raw-html-m2r:`<br>`
`Installation, administration, user & developer guide <http://www.redmine.org/projects/redmine/wiki/Guide>`_\ :raw-html-m2r:`<br>`
`Guide in Japanese <http://redmine.jp/guide/>`_\ :raw-html-m2r:`<br>`
`Recommended literature <https://www.packtpub.com/application-development/mastering-redmine>`_
