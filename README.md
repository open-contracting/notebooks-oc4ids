# OC4IDS Database Notebooks

A collection of notebooks for storing and querying OC4IDS data in a database.

Use the buttons below to open notebooks from the default branch:

Notebook | Colab link | Description
-- | -- | --
[Check and Import](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_Database_Data_Import.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/open-contracting/oc4ids_database/blob/main/OC4IDS_Database_Data_Import.ipynb) | Use this notebook to check data using the OC4IDS DRT and import the data and check results into the OC4IDS database.
[Delete Collections](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_Database_Delete_Collections.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/open-contracting/oc4ids_database/blob/main/OC4IDS_Database_Delete_Collections.ipynb) | Use this notebook to delete collections from the database.
[Data Feedback](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_Data_Feedback_Notebook.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/open-contracting/oc4ids_database/blob/main/OC4IDS_Data_Feedback_Notebook.ipynb) | Use this notebook to provide feedback on OC4IDS data.
[Indicator Coverage](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_Indicator_Coverage.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_Indicator_Coverage.ipynb) | Use this notebook to calculate the coverage of the [OC4IDS Indicators](https://docs.google.com/spreadsheets/d/1Vo6-Jis-J61PB_33QQx1YKnmQnI7M4rTq6CTMckAFsE/edit#gid=882740051).
[Indicator Coverage](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_CoST_IDS_Coverage.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_CoST_IDS_Coverage.ipynb) | Use this notebook to calculate the coverage of the CoST Infrastructure Data Standard (IDS), according to the [CoST IDS to OC4IDS mapping](https://standard.open-contracting.org/infrastructure/latest/en/cost/#cost-ids-to-oc4ids-mapping).

To open a notebook from a different branch, use Colab's [Github browser](https://colab.research.google.com/github/open-contracting/oc4ids_database/) to choose the notebook and branch you want to open.

Alternatively, you can use the Open in Colab browser extension ([Chrome](https://chrome.google.com/webstore/detail/open-in-colab/), [Firefox](https://addons.mozilla.org/en-US/firefox/addon/open-in-colab/)) to add a button that, when clicked when viewing a Jupyter notebook on github, will open that notebook in Colab.

## Database structure

![Database structure](database_structure.png)

## Contributing

### Edit a copy of a notebook

1. Open the notebook in Colab.

2. Make your changes in Colab, following the [style guide for SQL statements](https://ocp-software-handbook.readthedocs.io/en/latest/python/code.html#sql-statements).

3. Format any SQL code you add or edit using [pgFormatter](http://sqlformat.darold.net/).

### Commit your changes

1. [Create a branch](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/creating-and-deleting-branches-within-your-repository#creating-a-branch).

In Colab:

2. Click Edit -> Clear all outputs.

3. Remove any database credentials you entered into the notebook.

4. Click File -> Save a copy in Github.

4. Uncheck 'Include a link to Colaboratory'

5. Select your branch, enter a commit message and click OK.

### Request a review

1. [Create a pull request](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request).

2. Request a review from a helpdesk analyst.

3. If the reviewer requests changes, make the changes then repeat this step.

### Merge your changes

Once approved, you can merge your changes yourself.

## Reviewing

### Review changes

[Review the changes](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/reviewing-proposed-changes-in-a-pull-request).

For small changes, you can review the raw diff in the Github review interface.

For larger changes, you can review and comment on a visual diff by clicking the <img align="absmiddle"  alt="ReviewNB" height="28" class="BotMessageButtonImage" src="https://raw.githubusercontent.com/ReviewNB/support/master/images/button_reviewnb.png"/> button. You need to authorize the app the first time you open it.
