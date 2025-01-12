# Bingley Music Town Website

## About
This is a static site that displays at http://bingleymusictown.org.uk/

## Deployment
It is deployed via the main branch of this repo using Github Pages.

### Cron update
We rebuild the site once a day using the GitHub API and [gh-cli](https://cli.github.com/) tool, using a cron job on another server (David's). 

This means our 'Live music today' and 'Make Music Today' data is up to date.

Use the script under 

    /scripts/update_jekyll_sites.sh.example

to set up a cron job. The job is logged in a jekyll_update.log file locally.

This requires a GitHub authentication token which expires every now and again. If you need to replace that token you do it on the server that is running the cron job in the above file.

## Local Development
To run this locally

Clone the repository

	cd bingley-music-town
	bundle exec jekyll serve

## Generating individual organisation pages
We generate markdown for each organisation page by using the pagemaster plugin/gem.

This creates pages from a .yml data file, in this case, organisations.yml, by running 

    bundle exec jekyll pagemaster {collection name}

In practice we use: 

    bundle exec jekyll pagemaster organisations

This command will generate markdown for views for each item in the collection under ./_{collection name}

To update generated markdown delete that directory as existing files are not updated when pagemaster runs

### Adding an organisation
* Add the data in `_data/organisations.yml`
* Add a logo to `/assets/images/logos`
* Add an image if you have one to `/assets/images/organisations` 
* Build the page with `bundle exec jekyll pagemaster organisations`

### Updating Information about an organisation
* Update the data in `_data/organisations.yml`
* Delete the corresponding entry under `/_organisations` (or the entire _organisations directory)
* rebuild that page(s)with `bundle exec jekyll pagemaster organisations`

## What if my organisation is also a venue

Create an organisation in organisations.yml - you don't need to complete all fields

we use the `jekyll-redirect-from` gem to redirect the org page to a venue page. 
See Cottingley Community Centre as an example.

## Generating individual venue pages
We generate markdown for each venue page by using the pagemaster plugin/gem.

This creates pages from a .yml data file, in this case, venues.yml, by running 

    bundle exec jekyll pagemaster {collection name}

In practice we use: 

    bundle exec jekyll pagemaster venues

This command will generate markdown for views for each item in the collection under ./_{collection name}

To update generated markdown delete that directory as existing files are not updated when pagemaster runs

### Adding an venue
* Add the data to our Google Spreadsheet - see below.
* Run the scripts/fetch_events_data.sh to fetch the data from the spreadsheet
* The data will now be in `_data/venues.yml`
* Build the page with `bundle exec jekyll pagemaster venues`

### Updating Information about a venue
* Update the data in the Google Spreadsheet
* Run the scripts/fetch_events_data.sh to fetch the data from the spreadsheet
* The data will now be in `_data/venues.yml`
* Delete the corresponding entry under `/_venues` (or the entire _venues directory)
* rebuild that page(s)with `bundle exec jekyll pagemaster venues`

## Internal links between organisations and venues

Put the path and the link text in the venues spreadsheet, and/or in the organisations.yml file.

Examples of both exist. 

## Generating help pages and cards
We generate markdown for each help page by using the pagemaster plugin/gem.

This creates pages from a .yml data file, in this case, help.yml, by running 

    bundle exec jekyll pagemaster {collection name}

In practice we use: 

    bundle exec jekyll pagemaster help

This command will generate markdown for views for each item in the collection under ./_{collection name}

To update generated markdown delete that directory as existing files are not updated when pagemaster runs

### Adding a help
* Add the data in `_data/help.yml`
* Use the order field to help sort the items - lower numbers are first in lists
* Add images to `/assets/images/helps`
* Also create a resized image of max width 310px and with .resized.jpg as part of the file name
* Build the page with `bundle exec jekyll pagemaster help`

### Updating Information about a help
You can edit _help/<pagename> directly.
If you want to regenerate that page from the help.yml data, delete it first then run the pagemaster script.

### Helps on the Front Page
These are limited to the first 3 in the list as order by the order field.

## Images

### Banner images

Main banner images are currently 1280x800px

### Gallery

Gallery Images should be no wider than 1024px

Thumbnails should be 400px square - see below.

Place images in '/assets/images/gallery'

File names of the images are used to generate captions etc.

Underscores in filenames will be replaced by spaces in captions, titles and alt text. 

Run this script in that directory to generate Thumbnails
    
    find . \( -name '*.jpg' -or -name '*.JPG' \) -print0 |  while read -d $'\0' file ; do convert -define jpeg:size=400x400  "$file" -thumbnail 300x300^ -gravity center -extent 300x300  ../thumbnails/"$file" ; done

### Help Images

These images don't need to be wider then 350px.

### Instrument Images

These images don't need to be wider then 350px.

### Logos in the pre-footer area
These are included in the `index.md` file from `_include/gallery.html` and should show all the logos that we have.

## Live music, previous events listings
These are generated from public google spreadsheets:
https://docs.google.com/spreadsheets/d/1-Eugy7Wfl0O2dSach2D2dOoE8JEW2tI3sqChuCvLUYg/edit

Fetch the data with the script:

    #Fetch the data from those sheets.
    cd scripts
    ./fetch_events_data.sh 
    
    #Fetch and commit changes
    ./fetch_events_data.sh update
    
    #Fetch and commit and push changes to origin main
    ./fetch_events_data.sh update push

We use the data to build a live listing page, a previous events page, and to populate the front page with live events today.

## Instruments
Data is held in a google spreadsheet. Google forms collect the basic info.
We need to download the instruments sheet as a csv to update the website.
We could make this a public sheet and automate it.
Images are saved and named and then the filename is added to the spreadsheet

## Contributing
Please fork the repo,and make pull requests from your clone to this one.

### Branches
- `main` holds the most recently deployed code
- `(number)-(name)` branches are working branches where (number) is an issue number and (name) is made up, but has some relation to the issue

### Workflow
* Pick (or create an issue)
* Create a branch (from main if practical) - name the branch (issue number)-(suitable name) e.g. 23-fix-the-footer
* Work on the branch.
* Push to your own fork
* Make a pull request in this repo.