<p align="center">
    <a href="" rel="noopener">
        <img width="700" height="400" src="https://github.com/OUGC-Network/OUGC-Custom-Promotion-Field/assets/1786584/7ce0049f-cfb8-4322-86cc-4a7951ba987b" alt="Project logo">
    </a>
</p>

<h3 align="center">OUGC Custom Promotion Field</h3>

<div align="center">

[![Status](https://img.shields.io/badge/status-active-success.svg)]()
[![GitHub Issues](https://img.shields.io/github/issues/OUGC-Network/OUGC-Custom-Promotion-Field.svg)](./issues)
[![GitHub Pull Requests](https://img.shields.io/github/issues-pr/OUGC-Network/OUGC-Custom-Promotion-Field.svg)](./pulls)
[![License](https://img.shields.io/badge/license-GPL-blue)](/LICENSE)

</div>

---

<p align="center"> Allow administrators to select custom table fields to consider for group promotions.
    <br> 
</p>

## 📜 Table of Contents <a name = "table_of_contents"></a>

- [About](#about)
- [Getting Started](#getting_started)
    - [Dependencies](#dependencies)
    - [File Structure](#file_structure)
    - [Install](#install)
    - [Update](#update)
- [Usage](#usage)
- [Built Using](#built_using)
- [Authors](#authors)
- [Acknowledgments](#acknowledgement)
- [Support & Feedback](#support)

## 🚀 About <a name = "about"></a>

With the Custom Promotion Field plugin, administrators can now select custom tables and columns for seamless validation
during group promotions. For a quick setup, a simple form simplifies the creation of a custom field, while an advanced
form with JSON scripting caters to more complex verification needs. Tailor validations to specific criteria, ensuring
precision in user group promotions, making MyBB's core group promotions more flexible to your forum's unique needs.

[Go up to Table of Contents](#table_of_contents)

## 📍 Getting Started <a name = "getting_started"></a>

The following information will assist you into getting a copy of this plugin up and running on your forum.

### Dependencies <a name = "dependencies"></a>

A setup that meets the following requirements is necessary to use this plugin.

- [MyBB](https://mybb.com/) >= 1.8
- PHP >= 7.0
- [PluginLibrary for MyBB](https://github.com/frostschutz/MyBB-PluginLibrary) >= 13

### File structure <a name = "file_structure"></a>

  ```
   .
   ├── inc
   │ ├── languages
   │ │ ├── english
   │ │ │ ├── admin
   │ │ │ │ ├── users_ougc_custompromotionfield.lang.php
   │ │ │ ├── users_ougc_custompromotionfield.lang.php
   │ ├── plugins
   │ │ ├── ougc_custompromotionfield.php
   ```

### Installing <a name = "install"></a>

Follow the next steps in order to install a copy of this plugin on your forum.

1. Download the latest package from the [MyBB Extend](https://community.mybb.com/mods.php?action=view&pid=1358) site or
   from the [repository releases](https://github.com/OUGC-Network/OUGC-Custom-Promotion-Field).
2. Upload the contents of the _Upload_ folder to your MyBB root directory.
3. Browse to _Configuration » Plugins_ and install this plugin by clicking _Install & Activate_.

### Updating <a name = "update"></a>

Follow the next steps in order to update your copy of this plugin.

1. Browse to _Configuration » Plugins_ and deactivate this plugin by clicking _Deactivate_.
2. Follow step 1 and 2 from the [Install](#install) section.
3. Browse to _Configuration » Plugins_ and activate this plugin by clicking _Activate_.

**If you are updating to <ins>1.8.36</ins> from any previous version:**

1. After following the above steps, make sure to review your settings and templates as they were updated for this
   version.

[Go up to Table of Contents](#table_of_contents)

## 📖 Usage <a name="usage"></a>

### Group Promotions

Read the following description to understand and design your custom promotions fields when managing your group
promotions.

1. Go to the Administrator Control Panel, add or update a group promotion from _Home » Users & Groups » Group
   Promotions_.

- **Custom Field: Table** `text`
    - _Select a custom table to consider for this promotion. This table has to exist within the forum table._
- **Custom Field: Column** `text`
    - _Select a custom column to consider for this promotion. This column has to exist within the selected table above._
- **Custom Field: Value** `text`
    - _Select the value to compare against for the selected column above._
- **Custom Field: JSON Script** `text`
    - _JSON compatible script to set complex promotion verifications. If set, the above
      single custom field will be added to the end of the following objects automatically. Read the documentation for
      more on
      this._

#### Example Basic Custom Field

To promote users based on their last post date use the following configuration:

- Custom Field: Table `users`
- Custom Field: Column `lastpost`
- Custom Field: Value `3` - `Months`

[Find more presets in the official forum.](https://ougc.network/module?faqs&filtertf_plugins_code=ougc_custompromotionfield)

### JSON Complex Custom Fields

The plugin accepts a JSON compatible script to process complex custom verifications. Below is a description of the
expected format.

- whereClauses `array`
    - tableName `alphaNumeric`
    - columnName `alphaNumeric`
    - columnValue `mixed`
        - Accepts single or multiple values (array)
    - columnOperator `comparisonOperator`
        - Allowed values: `>`, `>=`, `=`, `!=`, `<=`, `<`, `<=>`, `IN`, `NOT IN`, `LIKE`, `NOT LIKE`, `LOWER`,
          or `UPPER`
    - aggregateFunction `aggregateFunction`
        - Sanitized to float, integer, or string
        - Allowed values: `COUNT`, `MAX`, `MIN`, or `SUM`
    - aggregateAlias `alphaNumeric`
    - relationMainField `mixed`
        - Alphanumeric and dot `.` allowed.
    - relationSecondaryField `mixed`
        - Alphanumeric and dot `.` allowed.
- logicalOperator `logicalOperator`
    - Allowed values: `AND`, `OR`, or `XOR`

Because mixing probabilities are high and no validation is done on the script, it is possible to cause SQL errors which
should at most cause the specific group promotion to fail. Test carefully to make sure your script works.

#### Example JSON Complex Custom Verification

The following script should validate the following:

- Get users with 3 or more threads.
- Count only visible threads.
- Count only threads from forums which forum identifier (fid) is `2` OR `30`.

```JSON
{
  "whereClauses": [
    {
      "tableName": "threads",
      "columnName": "tid",
      "columnValue": 3,
      "columnOperator": ">=",
      "aggregateFunction": "COUNT",
      "aggregateAlias": "totalThreads"
    },
    {
      "tableName": "threads",
      "columnName": "visible",
      "columnValue": 1,
      "columnOperator": "="
    },
    {
      "tableName": "threads",
      "columnName": "fid",
      "columnValue": [
        2,
        30
      ],
      "columnOperator": "IN"
    }
  ],
  "logicalOperator": "AND"
}
```

##### User Reported 10 Posts

```JSON
{
  "whereClauses": [
    {
      "tableName": "reportedcontent",
      "columnName": "rid",
      "columnValue": 10,
      "columnOperator": ">=",
      "aggregateFunction": "COUNT",
      "aggregateAlias": "totalPostReports"
    },
    {
      "tableName": "reportedcontent",
      "columnName": "type",
      "columnValue": "post",
      "columnOperator": "="
    },
  ],
  "logicalOperator": "AND"
}
```

##### User Gave 10 Reputation Points

```JSON
{
  "whereClauses": [
    {
      "tableName": "reputation",
      "columnName": "reputation",
      "columnValue": 10,
      "columnOperator": ">=",
      "aggregateFunction": "SUM",
      "aggregateAlias": "totalReputationPoints"
    },
    {
      "tableName": "reputation",
      "columnName": "type",
      "columnValue": "post",
      "columnOperator": "=",
        "relationMainField": "u.adduid"
        "relationSecondaryField": "adduid"
    },
  ],
  "logicalOperator": "AND"
}
```

##### User Sent 10 Private Messages

```JSON
{
  "whereClauses": [
    {
      "tableName": "privatemessages",
      "columnName": "pmid",
      "columnValue": 10,
      "columnOperator": ">=",
      "aggregateFunction": "COUNT",
      "aggregateAlias": "totalPrivateMessages"
    },
    {
      "tableName": "privatemessages",
      "columnName": "type",
      "columnValue": "post",
      "columnOperator": "=",
    "relationMainField": "u.uid"
    "relationSecondaryField": "adduid"
    },
  ],
  "logicalOperator": "AND"
}
```

##### User Posted 10 Words

The following script should validate the following:

- Get only visible threads.
- Get only visible posts.
- Count only posts from forums which forum identifier (fid) is `3` OR `4`.
- Users have posted 10 or more words in total.

```JSON
{
  "whereClauses": [
    {
      "tableName": "threads",
      "columnName": "visible",
      "columnValue": 1,
      "columnOperator": "="
    },
    {
      "tableName": "posts",
      "columnName": "visible",
      "columnValue": 1,
      "columnOperator": "="
    },
    {
      "tableName": "posts",
      "columnName": "fid",
      "columnValue": [
        3,
        4
      ],
      "columnOperator": "IN"
    },
    {
      "finalizingClause": true,
      "tableName": "posts",
      "columnName": "message",
      "evaluationValue": 10,
      "evaluationOperator": ">=",
      "aggregateFunction": "GROUP_CONCAT",
      "aggregateAlias": "totalWords"
      "aggregateFunctionOptions": {
        "DISTINCT": true
      },
      "stripTags": {
        "allow_smilies": true
      },
      "evaluationFunction": "str_word_count",
      "evaluationFunctionArguments": {
        "string": "message",
        "format": 0,
        "characters": null
      },
      "evaluationResultFunction": "version_compare"
    }
  ],
  "logicalOperator": "AND"
}
```

##### User Posted Specific Words

The following script should validate the following:

- Users have posted the word `this` 5 or more times in total, OR
- Users have posted the word `erat` 20 or more times in total.

```JSON
{
  "whereClauses": [
    {
      "finalizingClause": true,
      "tableName": "posts",
      "columnName": "message",
      "evaluationValue": 5,
      "evaluationOperator": ">=",
      "aggregateFunction": "GROUP_CONCAT",
      "aggregateFunctionOptions": {
        "DISTINCT": true
      },
      "stripTags": true,
      "evaluationFunction": "substr_count",
      "evaluationFunctionArguments": {
        "haystack": "message",
        "needle": "this",
        "offset": 0,
        "length": null
      },
      "evaluationResultFunction": "version_compare"
    },
    {
      "finalizingClause": true,
      "tableName": "posts",
      "columnName": "message",
      "evaluationValue": 20,
      "evaluationOperator": ">=",
      "aggregateFunction": "GROUP_CONCAT",
      "aggregateFunctionOptions": {
        "DISTINCT": true
      },
      "stripTags": true,
      "evaluationFunction": "substr_count",
      "evaluationFunctionArguments": {
        "haystack": "message",
        "needle": "erat",
        "offset": 0,
        "length": null
      },
      "evaluationResultFunction": "version_compare"
    }
  ],
  "logicalOperator": "OR"
}
```

[Go up to Table of Contents](#table_of_contents)

## ⛏ Built Using <a name = "built_using"></a>

- [MyBB](https://mybb.com/) - Web Framework
- [MyBB PluginLibrary](https://github.com/frostschutz/MyBB-PluginLibrary) - A collection of useful functions for MyBB
- [PHP](https://www.php.net/) - Server Environment

[Go up to Table of Contents](#table_of_contents)

## ✍️ Authors <a name = "authors"></a>

- [@Omar G](https://github.com/Sama34) - Idea & Initial work

See also the list of [contributors](https://github.com/OUGC-Network/OUGC-Custom-Promotion-Field/contributors)
who participated
in this project.

[Go up to Table of Contents](#table_of_contents)

## 🎉 Acknowledgements <a name = "acknowledgement"></a>

- [The Documentation Compendium](https://github.com/kylelobo/The-Documentation-Compendium)

[Go up to Table of Contents](#table_of_contents)

## 🎈 Support & Feedback <a name="support"></a>

This is free development and any contribution is welcome. Get support or leave feedback at the
official [MyBB Community](https://community.mybb.com/thread-221815.html).

Thanks for downloading and using our plugins!

[Go up to Table of Contents](#table_of_contents)