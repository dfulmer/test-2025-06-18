---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
title: An Implementation Guide for Vendors

---

# Introduction

EDI invoices are machine-readable versions of invoices which, when provided by vendors to libraries, may be imported into the Alma Library Services Platform to automatically create an invoice in the system without the necessity of manually entering the invoice information. This is a guide for vendors to help them learn how to create and provide EDI invoices to libraries.

# A Simple EDI Invoice File
Here is an example of a simple EDI invoice file based on the following invoice with two lines.


| Invoice         |.                       |.               |.           |.             |
|----------------- |----------------------- |-------------- |----------- |------------- |
| Invoice number: | 202409239             |               | Vendor:   | Vendor Inc. |
| Invoice date:   | 09/23/2023             |               | Buyer:     | Library     |
|                 |                       |               |           |             |
| Line Number     | Title                 | Order Number | Quantity   | Price       |
| 1               | Washington D.C.       | POL-178651   | 1         | $10.00       |
| 2               | The Judgment of Paris | POL-178652   | 1         | $20.00       |
|                 |                       |               |           |             |
|                 |                       |               | Shipping: | $5.00       |
|                 |                       |               | Total:     | $35.00       |

Here is the plain text content of the EDI invoice file, named 202409239.edi:

```
UNA:+.? '
UNB+UNOC:3+VEND:31B+LIBRDMF:ZZ+240101:0000+110'
UNH++INVOIC:D:96A:UN:EAN008'
BGM+380+202409239+43'
DTM+137:20230923:102'
CUX+2:USD:4'
ALC+C++++DL::28:Freight Charges'
MOA+8:5'
LIN+1'
QTY+47:1'
MOA+203:10'
PRI+AAB:10'
RFF+LI:POL-178651'
LIN+2'
QTY+47:1'
MOA+203:20'
PRI+AAB:20'
RFF+LI:POL-178652'
UNS+S'
CNT+2:1'
MOA+9:35'
MOA+79:35'
UNT+23+1'
UNZ+1+1'
```

An EDI file contains a header, invoice information, line information for one or more invoice lines, and a trailer. These elements consist of segments separated by single quotes.

An EDI file will include a Vendor Code, a code assigned by the library that will appear in each EDI file (it is “VEND” in the above EDI invoice file). An EDI file will also include the following information from the invoice:
- Invoice Number
- Invoice Date
- Invoice shipping total
- Invoice Total

And for each line:

- Line number
- Quantity
- Line price
- At least one of these: 
    - Buyer's order line number
    - Vendor Reference Number
- FTX note (optional)

# A Spreadsheet for Creating EDI Invoice Files

This spreadsheet will aid you in understanding how valid EDI files are created. Just click on the link to this Google Sheet [https://docs.google.com/spreadsheets/d/1TiUlBiZo4fC-8aVvvBs3rRoedkmEm4TQopGms0f78Fs/copy](https://docs.google.com/spreadsheets/d/1TiUlBiZo4fC-8aVvvBs3rRoedkmEm4TQopGms0f78Fs/copy), click Make a copy, and then follow the instructions to enter data for an invoice. The EDI invoice file contents will be created in a cell using formulas and you can see what will change based on different invoice numbers, invoice dates, lines, prices, etc.

# File Retrieval
EDI Invoice files can be imported into Alma two different ways: manually via the user interface and automatically via FTP or SFTP server.

## Manually
With this method, files can be emailed to the library and imported into Alma one by one.

## FTP or SFTP
With this method the vendor provides the library with credentials to log in to an FTP or SFTP server where the vendor will place new EDI invoice files. The credentials must allow Alma to log in to the server, retrieve files, and rename files. Alma checks the server every day for new files. If a new file is found, the file will be imported into Alma and the name of the file on the server will be changed to something like this: 202409239.edi6381.VEND-1727151032658.handled. Once the name of the file is renamed with the “.handled” suffix that means the file has been imported by Alma and may be removed from the server. It is also okay to leave the file on the server indefinitely, since Alma will recognize any file with the “.handled” suffix as already retrieved.

# File Names
Files can have any name and any file extension. A good way of naming files and keeping them unique is to use the invoice number followed by the “.edi” file extension. So, an invoice with the number 123 would have an EDI invoice file with the name “123.edi”. There is a limit to the number of characters in file names. If a file name with extension is longer than 38 characters, then Alma will be unable to import the file with the error message “Input file does not exist”.

# EDI Invoice Setup
In order to establish a successful EDI invoice workflow between vendor and library the vendor should carefully review this implementation guide and then try to create an EDI invoice file to accompany one of its invoices. Then, send the EDI invoice file and a PDF of the invoice to the library so that the library can import the sample EDI invoice file into Alma using the manual method and then compare the results to the PDF of the invoice. The library can then provide feedback to the vendor.

# Firm Order Workflow versus Approval Plan Workflow
With a Firm Order Workflow, the library places an order with a vendor and both the vendor, and the library have the order number in their respective systems. With the Firm Order Workflow, the vendor can include the order number with each invoice line in an RFF+LI segment as a match point in the EDI invoice file. Here is an example of information about an invoice line from an EDI invoice file where both the vendor and the library have the order number in their respective systems. It is line number 1, quantity of 1, price $10, and the Buyer's order line number is POL-178651:

```
LIN+1'
QTY+47:1'
MOA+203:10'
PRI+AAB:10'
RFF+LI:POL-178651'
```

With an Approval Plan Workflow, the vendor sells material to the library without the library placing a specific order for each item. In this workflow the vendor will generally send order level records or Embedded Order Data (EOD) MARC records to the library to be imported into the library system. The EOD records will include a Vendor Reference Number, and this number can also be used as a match point in the EDI invoice file. In this case the Vendor Reference Number should be in an RFF+SLI segment. Here is an example of information about an invoice line from an EDI invoice file where the vendor does not know the order number, but both the vendor and the library have the Vendor Reference Number. It is line number 1, quantity of 1, price $10, and the Vendor Reference Number is 123:

```
LIN+1'
QTY+47:1'
MOA+203:10'
PRI+AAB:10'
RFF+SLI:123'
```

# More Documentation
Here is some more in-depth documentation about Alma and EDI invoices.

EDI integration in Alma
[https://developers.exlibrisgroup.com/alma/integrations/edi/](https://developers.exlibrisgroup.com/alma/integrations/edi/)

Invoices
[https://developers.exlibrisgroup.com/alma/integrations/edi/invoice/](https://developers.exlibrisgroup.com/alma/integrations/edi/invoice/)
