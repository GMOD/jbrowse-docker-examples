# jbrowse-docker-examples
Collection of JBrowse docker examples

Note that this project DOES NOT make use of the JBrowse docker container in https://github.com/GMOD/jbrowse-docker.

This repo exists to form a clearing house of several projects that make use of two Docker base images for fascilitating GFF processing for JBrowse 
(either into tabix-index GFF or nested containment list (NCList) json) and for creating lightweight JBrowse server containers.

Docker Base Images
==================

GFF processing base image
-------------------------

The jbrowse-gff-base image at Docker Hub (https://hub.docker.com/r/gmod/jbrowse-gff-base) has several prerequisites for processing GFF data 
installed to make processing data easier.  Software includes:

* JBrowse (to provide flatfile-to-json.pl and generate-names.pl)
* GenomeTools for validating and sorting GFF
* SAMTools (to get bgzip and tabix)
* BioPerl
* AWS command line interface (CLI) (for optionally placing resulting data in an S3 bucket)
* git

The Dockerfile defining this base image is in the Alliance of Genome Resources GitHub repo for JBrowse containers (https://github.com/alliance-genome/agr_jbrowse_container) at https://github.com/alliance-genome/agr_jbrowse_container/blob/master/Dockerfile.processenv.

As with everything on this page, I would be willing to include additional software to make processing easier, so pull requests are welcome.

JBrowse server base image
-------------------------

The JBrowse server base image at Docker Hub (https://hub.docker.com/r/gmod/jbrowse-buildenv) has prerequisites for building a JBrowse server, including:

* nginx
* nodejs
* git

The Dockerfile defining this base image is also in the Alliance of Genome Resources GitHub repo for JBrowse containers (https://github.com/alliance-genome/agr_jbrowse_container) at https://github.com/alliance-genome/agr_jbrowse_container/blob/master/Dockerfile.buildenv. Again, PRs welcome!

Example Implementations
=======================

Outline:
* Starting with `FROM gmod/jbrowse-gff-base:latest` write a simple Dockerfile:
  * Pull in any configuration (minimun of a refSeq.json) from somewhere (github is easy but not required)
  * Pull in a build script that does the actual work
  * If you want the data to end up in AWS S3, pull in https://github.com/alliance-genome/agr_jbrowse_config.git, which provides a script called upload_to_S3.pl
* Arguments for the controlling CMD script should be passed in as environment variables.  For example, typical build and run commands would look like this:

```
docker build --no-cache -f Dockerfile.processgff -t processgff .
docker run --rm -e "AWS_ACCESS_KEY=<AWSACCESS>" -e "AWS_SECRET_KEY=<AWSSECRET>" processgff
```
Of course the AWS keys would be replaced with the real thing. Make sure to never commit your AWS keys to GitHub!

* The format of the controlling script can be quite simple:
  * Process the env variables
  * Use wget or curl to get the GFF file(s)
  * Process (with potential preprocessing steps) with either flatfile-to-json.pl or bgzip/tabix
  * Put the data somewhere (in these examples it's using upload_to_S3.pl and AWS S3)

Building the server container is moderately more complicated but not too bad.  

* Start with`FROM gmod/jbrowse-buildenv:latest as build` and write a simple Docker file:
  * Pull in needed code, including:
    * The JBrowse source
    * Configuration files
    * Any plugins that are needed
  * Move files around to put them where they need to (plugins to the plugin directory, config files in the data directory, etc)
  * Run `setup.sh -f` (the `-f` gives a faster build, as it skips Perl prereqs and examples)
  * Start a new container like `FROM nginx:latest as production`
  * Copy the JBrowse files and directories into the new container, like this:
```
COPY --from=build /usr/share/nginx/html/jbrowse/dist /usr/share/nginx/html/jbrowse/dist
```
The separate "FROM" lines result in a server container that is very small, since it only has nginx and the JBrowse JavaScript and configuration files to run the website and the data reside elsewhere.

Examples
========

WormBase GFF processing
https://github.com/WormBase/website-jbrowse-gff

ZFIN GFF processing
https://github.com/alliance-genome/agr_jbrowse_zfin/blob/main/Dockerfile.processgff

WormBase JBrowse server
https://github.com/WormBase/website-genome-browsers/blob/jbrowse-production/jbrowse/Dockerfile
* plus a working implementation at Docker Hub: https://hub.docker.com/r/gmod/wormbase-jbrowse

WormBase "protein schematic" JBrowse server (uses amino acid coordinates)
https://github.com/WormBase/website-genome-browsers/blob/protein_schematic_production/protein_schematic/Dockerfile
* plus a working implementation at Docker Hub: https://hub.docker.com/r/gmod/wormbase-jbrowse-protein

ZFIN JBrowse server
https://github.com/alliance-genome/agr_jbrowse_zfin/blob/main/Dockerfile

FlyBase JBrowse server
https://github.com/scottcain/flybase-jbrowse

Alliance of Genome Resources JBrowse server
https://github.com/alliance-genome/agr_jbrowse_container/blob/master/Dockerfile

SARS-CoV-2 browser (hosted at http://covid19.jbrowse.org)
https://github.com/GMOD/sars-cov-2-jbrowse
* plus a working implementation at Docker Hub: https://hub.docker.com/r/gmod/sars-cov-2-jbrowse
