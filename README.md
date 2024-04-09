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

<img src="https://github.com/kamccallum/videomatica-database/blob/main/copyright.png?raw=true" alt="copyright" width="80">


This project was produced by UBC iSchool graduate students Bryn Shaffer, Kyla McCallum & Stephanie Hohn for the course LIBR 554 002 2023W2 in April 2024. They retain their copyright to this project and received no funding for its completion. This license enables reusers to distribute, remix, adapt, and build upon the material in any medium or format for noncommercial purposes only, and only so long as attribution is given to the creator. If you remix, adapt, or build upon the material, you must license the modified material under identical terms. CC BY-NC-SA includes the following elements:

BY: credit must be given to the creator.
NC: Only noncommercial uses of the work are permitted.
SA: Adaptations must be shared under the same terms.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->

## Contact

Bryn Shaffer: bryn.shaffer@ubc.ca <br>
Kyla McCallum: ky.mccall307@gmail.com <br>
Stephanie Hohn: hohn.steph@gmail.com <br>
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
