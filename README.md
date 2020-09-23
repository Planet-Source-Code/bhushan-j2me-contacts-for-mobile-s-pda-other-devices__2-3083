<div align="center">

## J2ME Contacts for Mobile's, PDA & other Devices


</div>

### Description

Mobile phones enable you to enter a list of phone numbers that

You commonly use, along with the name of the person

Associated with the number.

Although this primitive contact list is useful for

Making calls for familiar friends and business associates.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Bhushan\-](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/bhushan.md)
**Level**          |Advanced
**User Rating**    |4.4 (133 globes from 30 users)
**Compatibility**  |Java \(JDK 1\.2\)
**Category**       |[Classes](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/classes__2-83.md)
**World**          |[Java](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/java.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/bhushan-j2me-contacts-for-mobile-s-pda-other-devices__2-3083/archive/master.zip)





### Source Code

```
/* Save this file Contacts.java
* Start the code
*
*/
import javax.microedition.midlet.*;
import javax.microedition.lcdui.*;
import java.util.*;
import java.io.*;
import javax.microedition.io.*;
import javax.microedition.rms.*;
public class Contacts extends MIDlet implements CommandListener {
 private Command exitCommand, addCommand, backCommand, saveCommand,
 deleteCommand;
 private Display display;
 private List mainScreen;
 private Form contactScreen;
 private TextField nameField, companyField, phoneField, faxField;
 private ChoiceGroup contactType;
 private boolean editing = false;
 private ContactDB db = null;
 private Vector contactIDs = new Vector();
 private Image[] typeImages;
 public Contacts() {
 // Get the Display object for the MIDlet
 display = Display.getDisplay(this);
 // Create the commands
 exitCommand = new Command("Exit", Command.EXIT, 2);
 addCommand = new Command("Add", Command.SCREEN, 3);
 backCommand = new Command("Back", Command.BACK, 2);
 saveCommand = new Command("Save", Command.OK, 3);
 deleteCommand = new Command("Delete", Command.SCREEN, 3);
 // Create the main screen
 mainScreen = new List("Contacts", List.IMPLICIT);
 // Set the Exit and Add commands for the main screen
 mainScreen.addCommand(exitCommand);
 mainScreen.addCommand(addCommand);
 mainScreen.setCommandListener(this);
 // Create the contact screen
 contactScreen = new Form("Contact Info");
 nameField = new TextField("Name", "", 30, TextField.ANY);
 contactScreen.append(nameField);
 companyField = new TextField("Company", "", 15, TextField.ANY);
 contactScreen.append(companyField);
 phoneField = new TextField("Phone", "", 10, TextField.PHONENUMBER);
 contactScreen.append(phoneField);
 faxField = new TextField("Fax", "", 10, TextField.PHONENUMBER);
 contactScreen.append(faxField);
 String[] choices = { "Personal", "Business", "Family", "Other" };
 contactType = new ChoiceGroup("Type", Choice.EXCLUSIVE, choices, null);
 contactScreen.append(contactType);
 // Set the Back, Save, and Delete commands for the contact screen
 contactScreen.addCommand(backCommand);
 contactScreen.addCommand(saveCommand);
 contactScreen.addCommand(deleteCommand);
 contactScreen.setCommandListener(this);
 // Load the type images
 try {
  typeImages = new Image[4];
  typeImages[0] = Image.createImage("/Personal.png");
  typeImages[1] = Image.createImage("/Business.png");
  typeImages[2] = Image.createImage("/Family.png");
  typeImages[3] = Image.createImage("/Other.png");
 }
 catch (IOException e) {
  System.err.println("EXCEPTION: Failed loading images!");
 }
 // Open the contact database
 try {
  db = new ContactDB("contacts");
 }
 catch(Exception e) {
  System.err.println("EXCEPTION: Problem opening the database.");
 }
 // Read through the database and build a list of record IDs
 RecordEnumeration records = null;
 try {
  records = db.enumerateContactRecords();
  while(records.hasNextElement())
  contactIDs.addElement(new Integer(records.nextRecordId()));
 }
 catch(Exception e) {
  System.err.println("EXCEPTION: Problem reading the contact records.");
 }
 // Read through the database and fill the contact list
 records.reset();
 try {
  while(records.hasNextElement()) {
  Contact contact = new Contact(records.nextRecord());
  mainScreen.append(contact.getName(), typeImages[contact.getType()]);
  }
 }
 catch(Exception e) {
  System.err.println("EXCEPTION: Problem reading the contact records.");
 }
 }
 public void startApp() throws MIDletStateChangeException {
 // Set the current display to the main screen
 display.setCurrent(mainScreen);
 }
 public void pauseApp() {
 }
 public void destroyApp(boolean unconditional) {
 // Close the contact database
 try {
  db.close();
 }
 catch(Exception e) {
  System.err.println("EXCEPTION: Problem closing the database.");
 }
 }
 public void commandAction(Command c, Displayable s) {
 if (c == exitCommand) {
  destroyApp(false);
  notifyDestroyed();
 }
 else if (c == addCommand) {
  // Clear the contact fields
  nameField.setString("");
  companyField.setString("");
  phoneField.setString("");
  faxField.setString("");
  contactType.setSelectedIndex(0, true);
  // Remove the Delete command from the contact screen
  contactScreen.removeCommand(deleteCommand);
  editing = false;
  // Set the current display to the contact screen
  display.setCurrent(contactScreen);
 }
 else if (c == List.SELECT_COMMAND) {
  // Get the record ID of the currently selected contact
  int index = mainScreen.getSelectedIndex();
  int id = ((Integer)contactIDs.elementAt(index)).intValue();
  // Retrieve the contact record from the database
  Contact contact = db.getContactRecord(id);
  // Initialize the contact fields
  nameField.setString(contact.getName());
  companyField.setString(contact.getCompany());
  phoneField.setString(contact.getPhone());
  faxField.setString(contact.getFax());
  contactType.setSelectedIndex(contact.getType(), true);
  // Add the Delete command to the contact screen
  contactScreen.addCommand(deleteCommand);
  editing = true;
  // Set the current display to the contact screen
  display.setCurrent(contactScreen);
 }
 else if (c == deleteCommand) {
  // Get the record ID of the currently selected contact
  int index = mainScreen.getSelectedIndex();
  int id = ((Integer)contactIDs.elementAt(index)).intValue();
  // Delete the contact record
  db.deleteContactRecord(id);
  contactIDs.removeElementAt(index);
  mainScreen.delete(index);
  // Set the current display back to the main screen
  display.setCurrent(mainScreen);
 }
 else if (c == backCommand) {
  // Set the current display back to the main screen
  display.setCurrent(mainScreen);
 }
 else if (c == saveCommand) {
  if (editing) {
  // Get the record ID of the currently selected contact
  int index = mainScreen.getSelectedIndex();
  int id = ((Integer)contactIDs.elementAt(index)).intValue();
  // Create a record for the contact and set it in the database
  Contact contact = new Contact(nameField.getString(), companyField.getString(),
   phoneField.getString(), faxField.getString(), contactType.getSelectedIndex());
  db.setContactRecord(id, contact.pack());
  mainScreen.set(index, contact.getName(), typeImages[contact.getType()]);
  }
  else {
  // Create a record for the contact and add it to the database
  Contact contact = new Contact(nameField.getString(), companyField.getString(),
   phoneField.getString(), faxField.getString(), contactType.getSelectedIndex());
  contactIDs.addElement(new Integer(db.addContactRecord(contact.pack())));
  mainScreen.append(contact.getName(), typeImages[contact.getType()]);
  }
  // Set the current display back to the main screen
  display.setCurrent(mainScreen);
 }
 }
}
/* END OF CODE Contacts.java
*
*/
/* Sava this file name as Contact.java
* Start the code
*/
import java.util.*;
public class Contact {
 private String name, company, phone, fax;
 private int type;
 public Contact(String n, String c, String p, String f, int t) {
 name = n;
 company = c;
 phone = p;
 fax = f;
 type = t;
 }
 public Contact(byte[] data) {
 unpack(new String(data));
 }
 public void unpack(String data) {
 int start = 0, end = data.indexOf(';');
 name = data.substring(start, end);
 start = end + 1;
 end = data.indexOf(';', start);
 company = data.substring(start, end);
 start = end + 1;
 end = data.indexOf(';', start);
 phone = data.substring(start, end);
 start = end + 1;
 end = data.indexOf(';', start);
 fax = data.substring(start, end);
 start = end + 1;
 type = Integer.parseInt(data.substring(start, data.length()));
 }
 public String pack() {
 return (name + ';' + company + ';' + phone + ';' + fax + ';' +
  ((String)Integer.toString(type)));
 }
 public String getName() {
 return name;
 }
 public String getCompany() {
 return company;
 }
 public String getPhone() {
 return phone;
 }
 public String getFax() {
 return fax;
 }
 public int getType() {
 return type;
 }
}
/* End of code Contact.java
*
*/
/* Save this file name as ContactDB.java
*
*/
import javax.microedition.rms.*;
import java.util.Enumeration;
import java.util.Vector;
import java.io.*;
public class ContactDB {
 RecordStore recordStore = null;
 public ContactDB(String name) {
 // Open the record store using the specified name
 try {
  recordStore = open(name);
 }
 catch(RecordStoreException e) {
  e.printStackTrace();
 }
 }
 public RecordStore open(String fileName) throws RecordStoreException {
 return RecordStore.openRecordStore(fileName, true);
 }
 public void close() throws RecordStoreNotOpenException,
 RecordStoreException {
 // If the record store is empty, delete the file
 if (recordStore.getNumRecords() == 0) {
  String fileName = recordStore.getName();
  recordStore.closeRecordStore();
  recordStore.deleteRecordStore(fileName);
 }
 else {
  // Otherwise, close the record store
  recordStore.closeRecordStore();
 }
 }
 public Contact getContactRecord(int id) {
 // Get the contact record from the record store
 try {
  return (new Contact(recordStore.getRecord(id)));
 }
 catch (RecordStoreException e) {
  e.printStackTrace();
 }
 return null;
 }
 public synchronized void setContactRecord(int id, String record) {
 // Convert the string record to an array of bytes
 byte[] bytes = record.getBytes();
 // Set the record in the record store
 try {
  recordStore.setRecord(id, bytes, 0, bytes.length);
 }
 catch (RecordStoreException e) {
  e.printStackTrace();
 }
 }
 public synchronized int addContactRecord(String record) {
 // Convert the string record to an array of bytes
 byte[] bytes = record.getBytes();
 // Add the byte array to the record store
 try {
  return recordStore.addRecord(bytes, 0, bytes.length);
 }
 catch (RecordStoreException e) {
  e.printStackTrace();
 }
 return -1;
 }
 public synchronized void deleteContactRecord(int id) {
 // Delete the contact record from the record store
 try {
  recordStore.deleteRecord(id);
 }
 catch (RecordStoreException e) {
  e.printStackTrace();
 }
 }
 public synchronized RecordEnumeration enumerateContactRecords()
 throws RecordStoreNotOpenException {
 return recordStore.enumerateRecords(null, null, false);
 }
}
/* End of code ContactDB.java
*
*/
```

