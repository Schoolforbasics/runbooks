### Implementation Engineering's Runbook PDFs

The Runbook repo is now a sphinx-based install configured to be built via sphinx-build's `make html`. When pushed to gitub.com, the master branch is automatically built and presented at [http://runbooks.readthedocs.io/](http://runbooks.readthedocs.io/).

####Requirements

- sphinx
- sphinx_bootstrap_theme

####Requirement installation

	conda install sphinx
	pip install sphinx_bootstrap_theme

####Updating Source

1. Update the corresponding RST file
2. Test with `make html`. This will build the html files in _build/html
3. Commit and push changes to github

####Fetching PDFs

When master is updated, the docs are cloned and built at [http://runbooks.readthedocs.io/](http://runbooks.readthedocs.io/). Readthedocs.org _can_ build PDFs, however they're not very customizable or easy to work with.
To create a PDF for a single runbook, do:

	wget -O AnacodaRepo.pdf "http://pdfmyurl.com/api?license=ol6be2SjTSQV&orientation=portrait&no_javascript&url=http://runbooks.readthedocs.io/en/latest/AnacondaRepo.html

This will send a request to http://pdfmyurl.com to generate a PDF from the supplied URL. Do this for each PDF you want to generate.

To generate PDFs for all runbooks, do:
	
	for i in AnacondaCluster AnacondaEnterpriseNotebooks AnacondaRepo;
		do wget -O  pdfs/$i.pdf "http://pdfmyurl.com/api?license=ol6be2SjTSQV&orientation=portrait&no_javascript&url=http://runbooks.readthedocs.io/en/sphinx-ified/$i.html";
		done