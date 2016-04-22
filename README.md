###Creating PDFs from Markdown pages

There are a dozen ways to convert Markdown docs to PDFs, but to stay consistent let's convert MD -> HTML -> PDF:

1. Install grip and wkhtmltopdf

		pip install grip wkhtmltopdf
		
2. Convert a Markdown doc to HTML:

		grip filename.md --export filename.html --title=

3. Convert an HTML doc to PDF:

		wkhtmltopdf filename.html pdfs/filename.pdf
		
###Batch convert all Markdown pages to PDF


Alternatively, we can batch convert all docs from MD -> HTML -> PDF:

		for i in *.md; do grip $i --export $(echo $i |sed 's/\.md//g').html --title=; done
	
		for i in *.html; do wkhtmltopdf $i pdfs/$(echo $i |sed 's/\.html//g').pdf; done