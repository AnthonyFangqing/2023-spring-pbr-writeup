# Admiral Shark
Yay a .pcapng
Time for Wireshark
Go download wireshark
Anyways, once we open wireshark, we can start following streams to see the information being sent.
 ![image](https://github.com/AnthonyFangqing/2023-spring-pbr-writeup/assets/77250066/0cdb8fe7-570d-4a16-adee-ffbb47cdfd61)

tcp stream 1 doesn’t seem particularly useful
 ![image](https://github.com/AnthonyFangqing/2023-spring-pbr-writeup/assets/77250066/ecc19725-d654-43f2-8d05-85cfc07fea2b)

 
tcp stream 2 seems like a jumbled mess at first. But, we do see some English in there.
drawing1.xml, sharedStrings.xml, theme1.xml, sheet1.xml
Seems like an Excel file (.xlsx). Many Microsoft file types like .docx and .xlsx are actually zipped collections of files, including lots of .xml files. “sheet1.xml” also suggests that this is an Excel file specifically. 
For example:
 ![image](https://github.com/AnthonyFangqing/2023-spring-pbr-writeup/assets/77250066/e6156d53-a86b-4499-92ab-e4f089b0914e)
![image](https://github.com/AnthonyFangqing/2023-spring-pbr-writeup/assets/77250066/21f24b8c-ba46-4178-b02e-56adf3a70982)

![image](https://github.com/AnthonyFangqing/2023-spring-pbr-writeup/assets/77250066/7e999052-658b-4683-aa60-ecc6c2bd3f51)

 
 
Assuming that this stream contains the full raw data for the Excel file, let’s download it and try to access its data. Because Wireshark is dumb, we need to select “Raw” from the “Show Data as” dropdown menu or else the saved file will be dumb and bad and very not useful.
Let’s try renaming it to an .xlsx file and opening it
 ![image](https://github.com/AnthonyFangqing/2023-spring-pbr-writeup/assets/77250066/741238e0-937f-4e9a-a4f8-3ef432d94b53)

Well that didn’t work. Now, we can do things the easy way or the hard way.
## Scuffed Way
If there’s a flag in this Excel file somewhere, it might be in the same place that text is stored normally in the spreadsheet cells.
 
However, I don’t know where text is stored in an .xlsx file.
In my test.xlsx, I put the word “baby” into the first cell. Let’s unzip test.xlsx and try searching for the location of “baby”.
 ![image](https://github.com/AnthonyFangqing/2023-spring-pbr-writeup/assets/77250066/92aaaa1e-33c8-4a2d-9b5a-c8fc25e8fd1d)

grep -r “baby” finds the xml file storing the cell data by searching through every file and subdirectory in the extracted .xlsx.
The data is in xl/sharedStrings.xml
Let's look there in the raw data we downloaded. Unzip the file and look for xl/sharedStrings.xml
![image](https://github.com/AnthonyFangqing/2023-spring-pbr-writeup/assets/77250066/67e2de5f-7f74-4ab1-9bfb-1e54e4c784ef)

Oh. That was easy

## Less Scuffed Way
binwalk the file, remove all the bad data [https://github.com/ReFirmLabs/binwalk]
Then, repackage the resulting files and open it normally as an Excel file.

