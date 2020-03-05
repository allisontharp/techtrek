---
title: 'Gunsmith Db and App Part 3.2: Acquisition Code'
tags:
  - gunsmith db
  - MySQL
url: 506.html
id: 506
categories:
  - Personal Projects
  - GunsmithDb
date: 2016-01-15 08:00:53
---

In [Part 3.1](http://www.techtrek.io/index.php/2016/01/12/gunsmith-db-and-app-part-3-1-acquisition-flow/), I showed the user flow for acquiring a gun.  In this portion, we will go over the VB code and MySQL commands to get the acquisition working.

### Search For a New Customer

##### General Overview

VB checks each of the textboxes in the customer search panel and adds those values to a string, which will be sent to MySQL as a SELECT query.  The result of this query is placed into a datasource which then is inserted into the DataGridView, which displays as a spreadsheet.

##### MySQL Query

The MySQL query needs to select the customer id (which is the foreign key for the customer table), the customer's name, company name, address, phone number, and license number.  No matter what the user is searching for, this portion of the query will always be true: \[sql\] SELECT customer.customer\_id, customer.firstname AS FirstName, customer.lastname AS LastName, customer.company AS Company, customer.address1, customer.address2, customer.city, customer.state, customer.zip, customer.phone\_number AS phone, customer.licensenum FROM customer WHERE \[/sql\]

##### VB Code

This query is put into a string variable in VB named 'query'.  The 'AS' portions of the query will rename the displayed column names in the GUI to be whatever is after 'AS'.  For instance, without the first 'AS', the first name column would be displayed as 'firstname' instead of 'FirstName'. After the WHERE clause, we need to put our search criteria.  However, we want a user to be able to search by anything, which means we don't necessarily know what needs to go after the WHERE clause. To get around this, I wrote a series of If statements that checks each of the textboxes within the customer search panel.  If the textbox has text in it, the query variable will be appended appropriately.  Every criteria after the first must have 'AND' at the beginning.  In order to check for this, I created a boolean variable, check, which checks to see if the current criteria is the first or not.  If it is the first, no 'AND' is added. The first few If statements are below.  All of the If statements are generally the same. \[vb\] If Not ACfname.Text = "" Then If Not check Then query += " firstname LIKE @firstname" Else query += " And firstname Like @firstname" End If check = True End If If Not AClname.Text = "" Then If Not check Then query += " lastname Like @lastname" Else query += " And lastname Like @lastname" End If check = True End If If Not ACcompany.Text = "" Then If Not check Then query += " company Like @company" Else query += " And company Like @company" End If check = True End If \[/vb\] After each textbox is checked, I then create a new MySQL Data Adapter and fill in the parameterized query to prevent SQL Injection (discussed in depth [here](http://www.techtrek.io/index.php/2016/01/05/sql-injection/)): \[vb\] adap = New MySqlDataAdapter(query, conn) If Not ACfname.Text = "" Then adap.SelectCommand.Parameters.AddWithValue("@firstname", "%" + ACfname.Text + "%") End If If Not AClname.Text = "" Then adap.SelectCommand.Parameters.AddWithValue("@lastname", "%" + AClname.Text + "%") End If If Not ACcompany.Text = "" Then adap.SelectCommand.Parameters.AddWithValue("@company", "%" + ACcompany.Text + "%") End If \[/vb\] Finally, I create the datasource and fill in the columns of the DataGridView.  I don't want the customer id to be visible since it isn't beneficial to the end user, so I hide that column. \[vb\] Try adap.Fill(ds) csearch\_grid.DataSource = ds.Tables(0) ds = Nothing da = Nothing csearch\_grid.Columns("customer_id").Visible = False Catch ex As MySqlException Finally conn.Close() End Try \[/vb\]

### Select A Customer

##### General Overview

There are two ways for the user to select a customer: double clicking the name or right clicking and selecting 'Select Customer'.  I created a function titled SelectCustomerAcqFunction so that I can reference one function for both methods. I select the row that the mouse is currently on, then grab the items in each column.  Since I disabled the ability to reorganize the table, I know what each column is and can easily set the text labels in the GUI for the selected customer.

##### VB Code

First, I get the current row and set the labels which are always required (customer idea and name).  From there, I check to make sure each column is filled.  If it is, I set the appropriate label.  The first few columns are shown below: \[vb\] Dim i As Integer Try i = csearch\_grid.CurrentRow.Index Catch End Try Dim cid As String = csearch\_grid.Item(0, i).Value SCcustomer\_id.Text = csearch\_grid.Item(0, i).Value SCfname.Text = csearch\_grid.Item(1, i).Value SClname.Text = csearch\_grid.Item(2, i).Value If csearch\_grid.Item(3, i).Value IsNot DBNull.Value Then SCcompany.Text = csearch\_grid.Item(3, i).Value End If If csearch\_grid.Item(4, i).Value IsNot DBNull.Value Then SCaddress1.Text = csearch\_grid.Item(4, i).Value End If If csearch\_grid.Item(5, i).Value IsNot DBNull.Value Then SCaddress2.Text = csearch\_grid.Item(5, i).Value End If If csearch\_grid.Item(6, i).Value IsNot DBNull.Value Then SCcity.Text = csearch\_grid.Item(6, i).Value End If \[/vb\]

### Firearm Dropdowns

##### General Overview

Next, the user must enter all of the firearm details.  All of the dropdowns are pulled directly from the database; this way, the dropdowns are always up to date.

##### VB Code

Since there are many dropdowns, I created a function so that I don't have to duplicate the same code many times.  The function has four inputs: the SQL query, the table that I want to query, the row I want from the query, and the dropdown to fill. \[vb\] Public Sub cbfill(sStmt As String, table As String, row As String, combobox As ComboBox) Dim cmd As New MySqlCommand(sStmt, conn) Dim da As MySqlDataAdapter = New MySqlDataAdapter(cmd) Dim dt As New DataTable(table) da.Fill(dt) If dt.Rows.Count > 0 Then combobox.DataSource = dt combobox.DisplayMember = row 'What is displayed combobox.ValueMember = row 'The ID of the row End If combobox.SelectedIndex = -1 End Sub \[/vb\] In order to fill the caliber dropdown, I simply call: \[vb\] cbfill("SELECT name FROM caliber", "caliber", "name", Dcaliber) \[/vb\]

### Submitting The Acquisition

##### General Overview

First, I check to make sure everything that is required is filled out.  If it is not, whatever is missing is put into an error message and displayed in the notification box.  Once everything that is required is filled out, a query is built and submitted to the database.

##### VB Code

In order to check if the required items are filled out, I check to see if there is anything in those textboxes.  If a required textbox is empty, the string variable 'errmsg' is appended with the appropriate warning.  The first few textboxes are checked below,, after I connect to the database: \[vb\] Dim errmsg As String = "" Try conn.Close() Catch ex As Exception End Try Try conn.Open() Catch ex As MySqlException MsgBox("An Error Occurred. " & ex.Number & “ – “ & ex.Message) End Try ' check that required things are filled out, vbcr if label, vbcrlf if textobx If SCcustomer\_id.Text = "cid" Then errmsg += vbCrLf + "Select Customer" End If If cat\_id Is Nothing Then errmsg += vbCrLf + "Category" End If If type\_id Is Nothing Then errmsg += vbCrLf + "Type" End If If cal\_id Is Nothing Then errmsg += vbCrLf + "Caliber" End If \[/vb\] In order to get a carriage return between each warning, I use `vbCrLf` If the variable 'errmsg' is not empty, the notification box gets filled with all of the warnings: \[vb\] Aerror.Text = "The following need to be completed:  " + errmsg \[/vb\] If everything is filled out properly, I submit it to the database.  There are two tables that must be submitted to: gun and acquisition.  We have to do these separately, as you cannot insert into multiple tables at the same time.

##### MySQL Queries

In order to submit to the gun table, we must at a minimum have the following query: \[sql\] INSERT INTO gun (gun\_id, type\_id, cal\_id, man\_id, model\_id, mancountry\_id, serialnum, date\_entered, cat\_id" \[/sql\] However, the user may have entered more information than this.  As with the customer search, I use several If statements to see what, if anything, should be added to the query: \[vb\] If Not Ayear.Text = "" Then query += ", year" End If If adeadline\_yes.Checked Then query += ", deadline" End If If Not Anotes.Text = "" Then query += ", note" End If \[/vb\] Finally, I close the end parenthesis and add the VALUES portion of the query: \[vb\] query += ") VALUES (NULL, @type\_id, @cal\_id, @man\_id, @model\_id, @mancountry\_id, @serialnum, NOW(), @cat\_id" \[/vb\] We must loop through the same textboxes again to add their values: \[vb\] If Not Ayear.Text = "" Then query += ", @year" End If If adeadline\_yes.Checked Then query += ", @deadline" End If If Not Anotes.Text = "" Then query += ", @note" End If query += ")" \[/vb\] I could potentially shorten the code and time by creating a query\_value string that adds the values in the first if statement loops.  This addition may be done in a further revision. Finally, we insert the values using the Parameterized Query to prevent SQL Injection and submit it to the database: \[vb\] cmd.Parameters.AddWithValue("@type\_id", type\_id) cmd.Parameters.AddWithValue("@cal\_id", cal\_id) cmd.Parameters.AddWithValue("@man\_id", man\_id) cmd.Parameters.AddWithValue("@model\_id", model\_id) cmd.Parameters.AddWithValue("@mancountry\_id", mancountry\_id) cmd.Parameters.AddWithValue("@serialnum", Aserialnum.Text) cmd.Parameters.AddWithValue("@cat\_id", cat\_id) cmd.Parameters.AddWithValue("@deadline", Adeadline.Value) cmd.Parameters.AddWithValue("@note", Anotes.Text) cmd.Parameters.AddWithValue("@year", Ayear.Text) Try cmd.ExecuteNonQuery() Catch ex As MySqlException Aerror.Text = ex.ToString() Finally End Try \[/vb\] Submitting to the acquisition table is the same.  However, one of the required columns in the acquisition table is the related gun\_id.  Since we just inserted the gun_id and it is auto incrementing, we _could_ just check for the largest gun_id.  However, this is bad practice.  A better solution is to use MySQL's `LAST_INSERT_ID` command.  This command will select the last primary key of the last insert from the _current user_. \[vb\] query = "Select LAST\_INSERT\_ID()" cmd = New MySqlCommand(query, conn) Dim reader As MySqlDataReader = cmd.ExecuteReader() Dim gun\_id As String Try While reader.Read() gun\_id = reader.GetString(0) End While reader.Close() Catch ex As MySqlException Aerror.Text = ex.ToString() Finally reader.Close() End Try \[/vb\] After we have the gun\_id, we can submit to the acquisition table using the same methods as above: \[vb\] query = "INSERT INTO acquisition (acq\_id, acq\_date, customer\_id, gun\_id, transaction\_date, date\_entered) VALUES (NULL, @acq\_date, @customer\_id, @gun\_id, @transaction\_date, NOW())" cmd = New MySqlCommand(query, conn) cmd.Parameters.AddWithValue("@acq\_date", AAcquired.Value) cmd.Parameters.AddWithValue("@customer\_id", SCcustomer\_id.Text) cmd.Parameters.AddWithValue("@gun\_id", gun\_id) cmd.Parameters.AddWithValue("@transaction_date", Atransdate.Value) Try cmd.ExecuteNonQuery() Catch ex As MySqlException Aerror.Text = ex.ToString() Finally ' conn.Close() End Try Aerror.Text = "Acquisition submitted" \[/vb\] Finally, we close the connection: \[vb\] conn.Close() \[/vb\] And that's it!  We successfully inserted an acquisition!  Click here to go to [Part 4.1: Disposition Flow](http://www.techtrek.io/gunsmith-db-and-app-part-4-1-disposition-flow/).