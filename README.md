# Process for updating NHS Digital Indicator Portal datasets for data.gov.uk

At present the following domains are included: NHSOF, CCGOIS, POMI.
Each month, the portal is updated. DGU can harvest the metadata for these domains, as long as there is an associated json file.
The indicator portal produces xml schemas for each indicator, so the general process is to convert the xml schemas into a json file capable of being read by DGU.
This is acheived by the following steps:
1. Combine the xml schemas for the individual indicators within each domain , so that there is one schema per domain
2. Utilise Google Refine to convert this xml to a json file
3. Save the json file to github
4. Refresh the harvests within DGU.


## 1. Combine the xml schemas

Utilise a combination of the portal indicator list (https://indicators.hscic.gov.uk/download/Indicator%20Portal%20indicators.xls) to get a list of the indicator IDs (eg. P02154 etc).

Append ".xml" to each

Create a string as follows: "P02154.xml+P02155.xml+P02156.xml......." so that all the IDs are included.

Use the command prompt to copy all of the xml files into on new, combined xml file:

In command prompt, the syntax is: copy P00001.xml+P00002.xml+.... P_OF.xml
where P_OF.xml is the new file to be created (P_NHSOF.xml, P_CCGOIS.xml, P_POMI.xml etc)

This creates a concatenation of all the files, however this is not a valid xml file - there are 2 further transformations required:

In a text editor (I prefer Notepad++):
1. find and replace all instances of 
	\<?xml version="1.0" encoding="UTF-8" ?\> 
	
	with ""
	
This removes each individual header. However, a single header is required for the overall file, so **\<?xml version="1.0" encoding="UTF-8" ?\>\<portal\>** should be added back at the very start. (Note the addition of \<portal\>

2. Add **\</portal\>** to the very end of the file
