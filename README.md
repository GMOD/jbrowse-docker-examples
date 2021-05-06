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
* AWS CLI (for optionally placing resulting data in an S3 bucket)
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

More details to come

WormBase GFF processing
https://github.com/WormBase/website-jbrowse-gff

ZFIN GFF processing
https://github.com/alliance-genome/agr_jbrowse_zfin/blob/main/Dockerfile.processgff

WormBase JBrowse server
https://github.com/WormBase/website-genome-browsers/blob/jbrowse-production/jbrowse/Dockerfile

ZFIN JBrowse server
https://github.com/alliance-genome/agr_jbrowse_zfin/blob/main/Dockerfile

FlyBase JBrowse server
https://github.com/scottcain/flybase-jbrowse

Alliance of Genome Resources JBrowse server
https://github.com/alliance-genome/agr_jbrowse_container/blob/master/Dockerfile

SARS-CoV-2 browser (hosted at http://covid19.jbrowse.org)
https://github.com/GMOD/sars-cov-2-jbrowse
