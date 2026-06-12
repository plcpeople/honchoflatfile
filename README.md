# honchoflatfile
Monitors a file, typically a CSV, for changes and reports values found in specific columns as numbers, mostly for use with the honcho program.

Usage:

(To test, create any CSV file on your D:\ or change the path, and output the line: 1,2,3,4,5)
```javascript
	var hff = require('./honchoflatfile.js');
	var conn = new hff({debug:false});
	var doneReading = false;
	var doneWriting = false;
	var now = new Date();

	conn.initiateConnection({path:'t:\\', ext: '.csv', debug: true, encoding: 'UCS-2', timeout: 45000, isNetwork: true }, connected);

	function connected() {
		conn.setTranslationCB(tagLookup);
		conn.addItems(['ONE','THREE']);
		conn.readAllItems(valuesReady);	
	}

	function valuesReady(anythingBad, results) {
		if (anythingBad) { console.log("SOMETHING WENT WRONG READING VALUES!!!!"); } else {console.log(results);}
		console.log("Value is " + conn.findItem('ONE').value + " quality is " + conn.findItem('ONE').quality);
		console.log("Value is " + conn.findItem('THREE').value + " quality is " + conn.findItem('THREE').quality);
		doneReading = true;
		//  process.exit();
		conn.readAllItems(valuesReady);
	}

	// This is a very simple "tag lookup" callback function that would eventually be replaced with either a database findOne(), or a large array in memory.  
	// Note that the return value is a controller absolute address and datatype specifier.  
	// If you want to use absolute addresses only, you can do that too.  
	function tagLookup(tag) {
		switch (tag) {
		case 'ONE':
			return 'R1'; // First value in the CSV, in real/float format
		case 'THREE':
			return 'R3'; // Third value in the CSV, in real/float format
		default:
			return undefined;
		}
	}
```
Test Procedure
If this file requires modification, follow the test procedures below to verify proper functionality of the HonchoFlatFile integration.

HonchoFlatFile Testing
Test #1 — Start Komodo Connection with No Folder Path Configured
Expected Results

- Service is running
- Webpage is running
- [Target Tag/Data Point] reports -999.00
- After an extended period without communication, the status changes to NO COM

Test #2 — Komodo Connection with Valid Folder Path but No CSV File Present
Expected Results

- Service is running
- Webpage is running
- [Target Tag/Data Point] reports -999.00
- After an extended period without communication, the status changes to NO COM

Test #3 — Valid Folder Path with CSV File Present but Not Updating
Expected Results

- Service is running
- Webpage is running
- [Target Tag/Data Point] reports -999.00
- After an extended period without new data, the status changes to NO COM

Test #4 — Change the CSV File Name
Expected Results

- Service is running
- Webpage is running
- [Target Point] detects the file name change
- The last known value remains active temporarily
- Communication is eventually lost if no new lines are added, resulting in a NO COM status

Test #5 — Append a New Line to the CSV File
Expected Results

- Service is running
- Webpage is running
- Newly added lines are detected and processed correctly
- Values update normally on the webpage

Test #6 — Add New Lines, Rename the File, Then Add Additional Lines
Expected Results

- Service is running
- Webpage is running
- New lines are processed correctly before the file rename
- File name changes are detected successfully
- New lines added after the rename are also processed correctly

