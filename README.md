WP CLI Import CSV
=================

**`importcsv` is a [WP CLI](http://wp-cli.org) command for flexible importing of content from .csv formatted files to WordPress.**

**Back up your database.**

* Version - 0.0.1-beta
* Author - [10up](http://10up.com) / [Jeff Sebring](http://jeffsebring.com)
* License - [GPLv3](http://www.gnu.org/licenses/gpl-3.0.html)

`importcsv` Subcommands
-----------------------

There are 2 subcommands included to check the validity of your arguments, and file header settings. `write` is the only subcommand which will write to the database. The terminal output allows you to review data mapping what will be imported before asking for confirmation of database write.

* `check` - validates import file headers
* `map` - maps data using headers ( uses `check` )
* `write` - import data to database ( uses `check` and `map` )

### Command Arguments & Syntax

The syntax and arguments are the same for all the subcommands.

Here is an example which would check file headers, then mapping of a file named `engineers.csv` ( in the current directory ) to be imported to the `pu01_engineers` post type, and set jennyengineer as the author. 

When setting up headers the first time, use the check command to only display initial file, post type, author and header settngs:

    wp importcsv check engineers.csv --post_type=<post type name> --author=<username or id>

Once headers validate, check the data mapping:

    wp importcsv map engineers.csv --post_type=<post type name> --author=<username or id>

If no error messages display, write to the database:

    wp importcsv write engineers.csv --post_type=<post type name> --author=<username or id>

*post type can be over-ridden using a correctly named header field*

### Optional Command Parameters

Specify a post type

    --post_type=<post type name>

Specify an author for posts

    --author=<username or id>

For importing and attaching of thumbnails ( multiple if desired ) to posts, either ensure the path in the database field is correct, or you can add a ( --thumbnail_path=url or relative path ) argument to prepend file names in a thumbnail field while sideloading into the media library.

    --thumbnail_path=<url or relative path>

CSV Import File Header Formatting
---------------------------------

Before you import, you'll need to format the header of your .csv file.

The importer uses the headers ( first line ) of files with specific formatting to determine how each column will be imported.

There are 3 parameters to set in each column header, separated by a hyphen ( - ):

* Data Type (post|meta|taxonomy|thumbnail)
* Sanitization/Escaping callback function (function must be defined)
* Field name (underscored delimited field name)

*** If a column is blank for some reason, simply name it's header 'blank'

### Data Types

#### Post

The post data type may only contain valid post fields. The data is added using `wp_insert_post()`

Example of a header for a field that would contain the post title:

    post-wp_kses_post-post_title

#### Meta

Meta data is attached to the inserted post using `add_post_meta()`. This example will attach the column of data to post meta data with the key `home_town`:

    meta-sanitize_key-home_town

#### Taxonomy

Attaches posts to any registered taxonomy using `wp_set_object_terms()`. This sets namespaced custom taxonomy, 'team':

    taxonomy-sanitize_title_with_dashes-pu01_team

*Only one term may be set at this time.*

#### Thumbnails

Thumbnails may be attached to posts, sideloaded from external url's or relative paths, depending on your needs and the values in your import file.

With this example, we use the specific name 'featured_image' to specify these images are to be attached as the default post thumbnail.

    thumbnail-absint-featured_image

For users of plugins like [Multiple Post Thumbnails](https://github.com/voceconnect/multi-post-thumbnails), other names will be set in post meta.

If we use the header parameters importing to our pu01_engineers post type:

    thumbnail-absint-secondary_image

The meta key will be the following:

    pu01_engineers_secondary_image_thumbnail_id


## Data Validation

The second parameter in each column header specifies the sanitization/validation callback used when saving data. The script will check that the functions are defined, but ultimately sanitization is up to you. This feature also gives you the ability to implement validation by creating your own sanitization functions.

## Field Names

The third and last parameter is field name. It is either a valid post field, meta key of your choice, namespace of your thumbnail image, or a taxonomy

## Useful WordPress Codex Pages

* [Post Types](http://codex.wordpress.org/Post_Types) & [Taxonomies](http://codex.wordpress.org/Taxonomies)
* [wp_insert_post](http://codex.wordpress.org/Function_Reference/wp_insert_post) - lists post data keys
* [Custom Fields](http://codex.wordpress.org/Custom_Fields)
* [Post Thumbnails](https://codex.wordpress.org/Post_Thumbnails)
* [Data Validation](http://codex.wordpress.org/Data_Validation)

## More WP CLI

For more information about the ultra powerful WP CLI, visit the website - http://wp-cli.org, or browse the tools here on Github - https://github.com/wp-cli.