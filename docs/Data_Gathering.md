# Obtaining the Data
<small> A summary of how we extracted our data from the original source and how we cleaned and condensed it.

**REQUIREMENTS BEFORE STARTING**
+ [GitHub account](https://github.com) created
+ [GitHub Desktop](https://desktop.github.com) client installed
+ [Atom](https://atom.io) editor installed
+ [Python 3.x](https://www.python.org/) installed

## Web Scapper

Our data comes from the national archives websites main page is located at aad.archives.gov. From the main page under the section labeled Genealogy/ Personal History we selected the section for Prisoners of War. And then when redirected to the next page where we were given in a list of record groups and the first one contains our data set for WW2 from 1942 to 1947. We then clicked to search in to that set. This link brings you to the description where we then had to click the search button again to be directed to the fielded search page for this data set. There are several options to sort the data and create a specified view of the data. The options for refining are: serial number, name, service, state of residence, area, status, detaining power, and camp. We did not use serial number, name, state of residence, camp, and area to sort and refine the data. We most utilized service code, status, and detaining power. There were 7 ways to navigate service code: Army, civilian, Marine, Merchant Marine, Navy, and war correspondent. Within status there were five categories to sort with: Died as Prisoner of War, Not Above Cases, Executed, died in Ship's Sinking or Result of Ship Sinking, shot While Attempting Escape, returned to Military Control, Liberated or Repatriated (this option is selectable 3 different times.) Detaining power only had two options and these are Japan and Germany.

![Image One](imgs/datagather/Selectors_with_HTML.PNG)
Above: Total List of Selectors to grab the text data

Originally, we were going to try and code a scrapper for this data set. We decided to both work on independent scrape methods to obtain the data. Curtis worked on the code in python, while I worked with the chrome extension WebScrapper. We found Webscrapper to be more accessible and efficient for our project and we chose to move forward with this method. There were several scrapes we had to preform to get all the data that we concluded with. All the different scrapes utilized the same sitemap in WebScrapper. When the sitemap is originally run there must be a page load delay set because the archives records page has a redirect. We must allow enough time for the page with valid HTML to appear. 1.8-5 seconds is a sufficient delay to allow for the redirect. Once the page has loaded the scrape can begin. At the root level of the sitemap there are two selectors: Link and Next Page. When the initial page loads in to the ghost window the first selector, Link, collects the first 50 links for each individual record. Then the second selector, Next Page, navigates the phantom window to the next page where Link will collect the next 50 records’ URLs. We were unable to use the same Next Page selector for the 2nd or any subsequent pages because the HTML changes for that link. We had to create a new selector which we labeled Next Page 2; this selector functioned exactly like Next Page. For the system to run on a loop through all the pages Next Page 2 had to be made a parent of itself, and its other child was Link. From the 2nd page on the scrape could collect each record’s link autonomously.

![Image Two](imgs/datagather/Next_page_with_HTML.PNG)
![Image Three](imgs/datagather/next_page_2.PNG)
Above The differences in HTML for each selector can be seen in the second column.

![Image Four](imgs/datagather/Selector_graph_pows.PNG)
Above is the selector graph for the full information scrape

Once all the links were collected each individual record was scrapped of its data. To retrieve each, field a different selector was created, the following are the 24 sectors that a were created. POW Transport Ships, Rep, Camp, Detaining Power, Status, Source of Report, Latest Report Date: Year, Latest Report Date: Month, Latest Report Date: Day, Area, Parent Unit Type, Parent Unit Number, Type of Organization, State of Residence, Racial Group Code, Date Report: Year, Date Report: Month, Date Report: Day, Arm or Service Code, Arm or Service, Service Code, Grade Code, Grade, Alpha, and Name.

We preformed several scrapes to gain the data. The first few we ran were merely test and we only yielded a few hundred results each. There were two sets of data we could directly download a CSV for, these were the War Correspondents and Merchant Marines, their records were less than 1,000 which was the limit to directly download from them. On the 5th or 6th try we were finally able to get the scrape to run through several hundred pages of records and that scrape yielded nearly 12,000 results. The scrape itself took roughly 48 hours. Added with the other smaller scrapes we preformed we had roughly 20,000 results of data. But we wanted to be able to have it all.  We ran another scrape that lasted 4 consecutive days and nights. It collected over 75,000 URLS. When the scrape completed the data from those URLS was unable to be downloaded and we lost all that data. It was confirmed through Dr. David Thomas that there was not enough local memory to hold that much information and it caused the program to crash when complete.

We decided that obtaining all 143,000 records would consume too much time and hurtful to our project so we preformed one last scrape that was altered to answer a specific question. We wanted to understand the relationship between where and how individuals died in comparison to their race. This scrape was run with a reduced amount of text selectors. We only looked at ten for this scrape: Camp, Detaining Power, Status, Source of Report, Area, Type of Organization, Racial Group Code, Arm or Service, Grade, Alpha, and Name. This scrape was performed in 3 different sections with approximately 92 pages of 50 links each. The scrapes took 6 hours individually to complete each. At the end we had a complete set of data for all that had died as POWs, totaling about 15,000 records. These sets had some overlap in where they scrapped so when we combined them in to one excel file we needed to delete any duplicates and we were easily able to do this through excel. The “Data” tab in the workbook listed an option to delete duplicates. We had to select the columns we wished to match and delete duplicates for, in this case we wanted all fields to match. After we completed this our number of records dropped to 13,801 which was accurate to what the original data source provided as the total.

![Image Five](imgs/datagather/Selector_graph_pows.PNG)
Above is the selector graph for the specialized scrape


## Cleaning our Data

We used open refine to clean out data and make it more friendly for Tableau to understand. For each column, we used the text facet feature to preform mass edits in the program. The first step in cleaning our data was to remove in blanks that existed and make any irregularities unified. For any fields that appeared as blank or null we switched them to Unidentified Code. The field that we used as our main classification was Arm or Service, which should have yielded Army, Navy, Marine, Civilian, Merchant Marine or War Correspondent. The fields or Navy, Marine, Merchant Marine and War Correspondent all compiled correctly but Army and civilian did not transfer correctly for all files. For Army, their specific rank and job appeared in this field while for Civilian they were merely left blank. It was simple to replace all the blanks with Civilian but Army took slightly longer. There were roughly 30 different inputs for Army and each group had to be selected and redone as civilian. This process took roughly 30 minutes for each of our different data sets, we did a quick search on each field to ensure they were supposed to be Army and not a different branch. There was also a section called “Bureau of Yards and Docks” which upon a quick search revealed it was a navy branch and we switched that over. When we thought the fields, we would be using were cleaned and unified we opened the first data set in Tableau and found that the way the “Area” tab which signified where POWs were detained was not in a format that was recognizable. They each had a “Theatre” specified e.x. European Theatre: Poland. We could remove these two different ways. The first was to use the prior method of consolidating the fields but I found a faster way just using excel. You utilize the “Ctrl+F” feature and you type in the theatre you wish to remove. For the prior example you Would type: “European Theatre: “ When doing this you must be sure to add the space after the colon so it is removed as well. There is then a tab called “Replace” which you would click and type nothing in to the replace section. Then you select all options and confirm. This will select all that start with “European Theatre: “ This was helpful because there would be multiple countries listed after the colon, in open refine each of these countries would have to be done individually.

## Making Changes

You can add/remove files to this repository folder using Explorer (Windows) or Finder (Mac) just like any other. But, for working on the meat of the project, the best way is to work inside of your plain text editor, [Atom](https://atom.io).

---

[![Link to Image](docs/imgs/included/screenshot.jpg)](docs/imgs/included/git_atom.jpg)

---

Assuming that you have installed Atom, you can open the project directly by clicking the 'repository' menu inside GitHub Desktop, and then choosing 'Open in Atom.' Depending on your system, you may also have an 'Open in Atom' shortcut button visible near the top right.

This project uses the Markdown language to generate the final webpages. Markdown files end in .md, and are edited in Atom, or some other plain-text editor. This is not the place for a full explanation of Markdown, but below are a couple useful links.
* For an overview on Markdown syntax, check out this [cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
* Try this in-browser [Markdown Editor](http://dillinger.io/), it has a live preview

## Committing and Pushing Changes

Once you have made changes to your local copy (see the 'Markdown Help section below for specifics'), you need to 'commit' all of these changes locally. Each time you commit, you create a milestone, preserving your project in different stages. Later, you can roll back to these stages if you need to.

To commit, open your GitHub Desktop client, and make sure you have the his4936-starter project selected on the left-hand tab. Next, click over to the 'Uncommitted Changes tab' near the top of your window (circled in the below picture). You will then see a list of all documents which are new or changed since the last commit (unless there are no changes). If you click any document, the right-hand pane will show the line-by-line breakdown of all changes in the document since the last commit.

---

[![Link to Image](docs/imgs/included/screenshot.jpg)](docs/imgs/included/git_commit.jpg)

---

Next, each commit must at least have a brief summary message which helps users keep track of all the different commits. Type in a (very) short message describing your changes. Finally, click the 'Commit to Master' (or whatever branch you are working on). You have now successfully committed your changes *locally*.

Although you have successfully committed to your own machine, you will eventually need to upload these changes to the remote server so that they can be viewed on GitHub. To do this, simply click the 'Sync' button near the top-right of the window. Now if you visit the repository on your GitHub account, you should see the changes. You do not have to push these changes to the remote server each time you commit. You can make as many commits locally as you want before syncing all of them to the remote GitHub server. This way, you can make sure everything is the way you want it on your machine, and only upload to GitHub when you are ready for others to access the files.

## Testing Your Project

You probably want to make sure everything looks as you want before uploading your changes to the server. Fortunately, you can use Python to run everything on your machine.

You need to make sure you have the necessary Python dependencies if you want to test the site on your computer. To install them, open your terminal/command prompt, navigate to the folder with this repository, and type `pip install -r requirements.txt`. If this does not work, try `pip3 install -r requirements.txt`

You will also need to edit the `mkdocs.yml` file in Atom (or other text editor). All of your pages must be located inside the docs directory (or subfolders). You should edit the existing index page (docs/index.md) to your liking, but do not delete or rename it.

To test your project, navigate to your project directory and type `mkdocs serve`. Then, go to your browser and enter `127.0.0.1:8000` into the URL bar. You should see your main index page.

## Working with Branches

When working as a team, changes made by one person might adversely affect the work of another person. That is why it is best to create different branches. To create a new branch, open your project in GitHub Desktop client and click 'Create New Branch' button (circled in the picture below). Type in the desired name of your new branch. Notice that there is a 'from' option. This tells it which branch acts as its 'parent'. Unless you already have made branches, right now you can only choose 'Master.' Then click 'Create Branch'.

---

[![Link to Image](docs/imgs/included/screenshot.jpg)](docs/imgs/included/git_branch_new.jpg)

---

You now have a new branch on your local machine. But not on the remote GitHub website. To do so, simply click the 'Sync' button again, and it should push your new branch to the remote server.

You can switch back and forth between which branch you are working on by clicking the button immediately to the right of the add new branch button. This will actively swap files in and out, not only in GitHub Desktop and Atom, but also in your Explorer/Finder window. If you can't find files or changes you know you have made, make sure you are on the right branch!

---

[![Link to Image](docs/imgs/included/screenshot.jpg)](docs/imgs/included/git_branch_remote.jpg)

---

To view the status of different branches on the remote server, head to the GitHub repository webpage. There, just above where the files are listed, you should see a button that says something like 'Branch: Master' (circled in the picture above). Click it to see a list of all the branches that have been created on the remote server. Choose one, and you can then view the files for that branch.

Eventually, you will probably want to merge the changes you make in one branch back to its parent. Say that I made a new branch called 'development'. While I am working on it, I don't want these files to replace the master branch. So, working on the development branch, I made changes to this branch on my local machine. In addition, at various times I synced these changes to the remote version of the development branch. Finally, after making a number of commits and pushes, I am satisfied with everything. Now, I want to merge all of these changes up to the master branch at once.

To do this, I need to create what is known as a 'Pull Request'. Simply click the button marked 'Pull Request' (just above the Sync button), and a new pane will Open. You can then set which branch pulls from which. You should have the branch you want the changes to merge INTO on the left (probably master, if it is your first attempt), and which branch you want the changes to come FROM on the right (in my case, development). Much like making a commit, I can then enter a short summary message and an optional longer description message.

We are almost done, the only remaining step is to approve the pull request on the remote GitHub website itself. Although anyone can generate a pull request, the owner of the repository (or authorized user) must approve it before changes will appear on the remote site. This gives a project manager a last chance to review everything before it becomes finalized.

---

[![Link to Image](docs/imgs/included/screenshot.jpg)](docs/imgs/included/git_pull.jpg)

---

Going to the website, click over to the 'Pull Requests' tab (circled above). If (and only if) a pull requests have been made, they will then be listed below. They should include the summary message, as well as which branches are involved in the request. Click the pull request you want to approve to get the details. If you are satisfied, click the 'merge pull request' request button, and then 'merge branches' (or something to that effect). The changes should now take effect.

**ADD SECTION HERE ON BRINGING CHANGES MADE FROM PARENT BRANCH INTO CHILD BRANCH**

## Team Strategies

* Only update the Master branch with fully polished changes
* Create a development branch from the master, only update it with changes you want the whole team to have access to
* Create sub-branches of the development branch for each team member (or for each section of the site)
* Each team member works on their own branch, only making pull requests to the development branch when they have changes ready that they want other team members to see.
* Once the development branch has been polished, the team leader then creates/approves a pull request to the master branch
