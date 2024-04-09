<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->

<a name="readme-top"></a>

<h3 align="center">Videomatica Database</h3>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#introduction">Introduction</a></li>
        <li><a href="#build-history">Build History</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
    </li>
    <li><a href="#important-parameters-and-restrictions">Important Parameters and Restrictions</a></li>
    <li>
        <a href="#using-the-database">Using the Database</a>
        <ul>
            <li><a href="#exported-sql-queries">Exported SQL Queries</a></li>
        </ul>
    </li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->

## About The Project

### Introduction

In 2011 Vancouver media rental store Videomatica became a retail-only store, donating 30,000 of its items to the UBC library and 2,800 to the SFU library. Neither library created a way to browse the Videomatica items as a unique collection. We decided to build a database that allows Videomatica to better work alongside the UBC and SFU libraries that now own pieces of its legacy collection, with the goals of offering users culturally rich and non-algorithmic media browsing, and preserving niche and independent media records.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Build History

This database has been built with SQLite which, while similar to SQL, has certain limitations you should be aware of. We recommend familiarizing yourself with these differences in syntax before working with our database.

One of the key limitations of SQLite is the inability to create stored procedures. However, you will note in browsing the database that there are sections for views and triggers, and one of each has been prepopulated to our database.

This database contains the following tables which capture the semantic and metadata information on copies of video based media items:

- Media item
- Actor
- Director
- Award
- Genre
- Language
- Comment
- Admin

Intermediary tables are used to connect entities to each other and inform specific record information, such as genres and languages for specific media items.

- Actor_Award
- Director_Award
- Media_Award
- Media_Actor
- Media_Director
- Media_Genre
- Media_Language

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->

## Getting Started

SQLite is a C-language library and the SQLite file format is stable, cross-platform, and backwards compatible. The source code is in the public domain and is free to everyone to use for any purpose. Copies of the prebuilt binaries for different operating systems and source code are available for download at sqlite.org. Users can open this file using the DB Browser for SQLite application available at https://sqlitebrowser.org/.

<!-- Important Parameters and Restrictions -->

## Important Parameters and Restrictions

The following important parameters and restrictions should be noted before using our database:

- Media Items in this database are defined as physical copies of media—whether physically present/available or not. This means that in dealing with copies of media each should be added as their own unique record, in order to track locations across institutions, as well as comment on the physical condition or uniqueness of items. Copies of media may use a unique ID condition where a dash and an integer is applied to their ID number.
- If you want to leave a comment, you first need to create an admin table record with your name before creating comments. This is because your comments will be linked to your admin using your unique admin ID, which is created when you add yourself to the admin table.
- This database aims to avoid using NULL data, and in many cases there is no option to leave attribute data blank. This is primarily because this database seeks to identify knowledge and metadata related to film records. As such ‘unknown’ should be used when data for a certain record is not known by the creator - this indicates to others this information is not known any could be added by someone with the required knowledge, rather than NULL which may convey to other users that the data is either yet to be added or was simply overlooked during record creation.
- The series name attribute of the media item table is used to indicate media that belong in the same series or franchise as other media in the database. It is critically important that the same spelling and format are applied when populating this attribute in order for the series name data to match across media items.
- The status attribute of the award table should only be populated once the records for the associated recipient (media, actor or director) has been added to the database. An award can exist prior to a recipient being linked; for example, future awards can be in the database and then have their status updated and linked to recipients.
- Over 40 written and spoken languages have been added to the database, including closed caption English and "silent." Please ensure at least 1 form of spoken language is added to each media item, even if that language is silent.
- When connecting genres to a media item using the media-genre intermediary table, the priority number should be populated to indicate the level of importance a genre has to the media, with 1 being the highest priority.
- If you are interested in providing "unknown" information for a record but do not have administrative privileges or want to provide corrective information about existing records, please consult the admin table for the designated administrators for the institutions where the media item is currently held.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- Using the Database -->

## Using the Database

To use the database, please follow these steps:

1. Download the SQLite DB Browser.
2. Unzip the database folder and extract the Videomatica db file.
3. Open the Videomatica db file in the DB Browser.
4. Open the Query files, if applicable to you.

You must add records in a certain order to avoid error messages. The order is as follows:

1. Media
2. Language, Genre, Director, Actor, Award. These have an interchangeable order, and you can also opt to add unassociated records that might be used in future. If populating the status attribute of Award which relates to a connected recipient of either director, actor, or media, it should be added after the respective recipient records.
3. Admin
4. Comments
5. Intermediary tables - Media_Actor, Media_Lang, Media_Genre, Media_Director, Media_Award, Director_Award, Actor_Award. These have an interchangeable order once tables on either end have records populated to be joined on.

Follow SQL syntax to add records.

```sh
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...);
```

For example, if inserting a media item:

```sh
INSERT INTO Media
VALUES (7, 'Under the Skin', 2013, 'DVD', 'University of British Columbia Library','standalone');
```

To find information about a specific media item, use the SELECT statement and WHERE clause. For example, if you want to know when the movie ‘Alien’ was released, you would use the following query:

```sh
SELECT * FROM Media WHERE media_name = "Alien"
```

But this only selects records from the Media table. If you want to know about the actors, directors, languages, genres, and awards that are associated with a particular movie, you would need to JOIN tables. The following query displays the director of the movie Alien.

```sh
SELECT m.media_name, d.first_name, d.last_name
FROM media m
JOIN media_director md ON m.media_ID = md.media_ID
JOIN director d ON md.director_ID = d.director_ID
WHERE m.media_name='Alien'
```

### Exported SQL Queries

If you want to view all movies that fit an attribute, such as spoken or subtitled in English, you would use the WHERE clause to filter results. The following query displays movies that are in the fantasy genre and either spoken or subtitled in English. This is the exported query called fantasy-and-english-search.

```sh
SELECT m.media_name, g.genre_name, mg.priority_numb, m.location,  l.lang_name, l.lang_role
FROM media_genre mg
JOIN media m ON mg.media_ID = m.media_ID
JOIN genre g ON mg.genre_ID = g.genre_ID
JOIN Media_Lang ml ON ml.media_ID = m.media_ID
JOIN language l ON l.lang_ID = ml.lang_ID
WHERE g.genre_name = "Fantasy" AND l.lang_name = "english"
ORDER BY mg.priority_numb ASC
```

The database also has a pre-loaded view and trigger. If you would like to know the actors and directors associated with all media items in the VHS format, SELECT all (\*) from vhs_media. This is the view. If you insert new media items, a log message will automatically generate with the date and media_id of the addition. This is the trigger.

The remaining query files are useful for administrators seeking to understand their collection. Number-of-prestigious-media displays the number of media items that have won more than 0 awards, grouped by location. Procedure-release-years creates a stored procedure to display the number of media items released within a given range of years. However, because SQLite does not accommodate stored procedures, this query will not run in the database. Instead, you would cut out everything else except the following line:

SELECT COUNT(CASE WHEN year BETWEEN ', min_year, ' AND ', max_year, ' THEN media_ID END) AS "', @column_heading, '" FROM media;'

Then, replace min_year, max_year, and @column_heading with the appropriate year, so a query applicable to SQLite would look like

```sh
SELECT COUNT(CASE WHEN year BETWEEN 1990 AND  2000 THEN media_ID END) AS "1990-2000" FROM media
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- LICENSE -->

## License

<img src="data:image/png;base64,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" alt="copyright" width="80">


This project was produced by UBC iSchool graduate students Bryn Shaffer, Kyla McCallum & Stephanie Hohn for the course LIBR 554 002 2023W2 in April 2024. They retain their copyright to this project and received no funding for its completion. This license enables reusers to distribute, remix, adapt, and build upon the material in any medium or format for noncommercial purposes only, and only so long as attribution is given to the creator. If you remix, adapt, or build upon the material, you must license the modified material under identical terms. CC BY-NC-SA includes the following elements:

BY: credit must be given to the creator.
NC: Only noncommercial uses of the work are permitted.
SA: Adaptations must be shared under the same terms.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->

## Contact

Bryn Shaffer: bryn.m.shaffer@gmail.com
Kyla McCallum: ky.mccall307@gmail.com
Stephanie Hohn: hohn.steph@gmail.com
UBC iSchool: Tel 604 822 2404

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACKNOWLEDGMENTS -->

## Acknowledgments

We thank Videomatica founders Graham Peat and Brian Bosworth, whose expert curation created Videomatica’s unique and rare media collection, and for contributing many items to UBC and SFU so that these pieces of Vancouver’s media history could continue to be available to the public.

We also thank our LIBR 554 002 2023W2 Database Design instructor Fatemeh Salehian Kia and teaching assistant Patrick Sales for their invaluable expertise.

SFU’s Videomatica Collection:
https://www.lib.sfu.ca/about/branches-depts/mrc/videomatica-documentary-collection

UBC’s Videomatica Collection:
https://collections.library.ubc.ca/featured-collections/videomatica/about/

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
