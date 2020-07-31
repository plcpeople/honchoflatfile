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

