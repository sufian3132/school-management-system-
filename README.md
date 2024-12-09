
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

class AttendanceNode {
public:
    string date,status;
    AttendanceNode* next;
    AttendanceNode(string d,string s) {
        date = d;
        status = s;
        next = NULL;
    }
};
    
class Node_s {
public:
    string Name;
    int ID;
    int clas;
    string DoB;
    string cnic;
    string phone;
    Node_s* next;
    Node_s* priv;
    AttendanceNode* attendanceHead;
    Node_s(string cn,string ph,string n,string dob ,int i, int c) {//parameterised constructor  for student 
        Name = n;
        ID = i;
        clas = c;
        DoB = dob;
        cnic = cn;
        phone = ph;
        next = NULL;
        priv = NULL;
        attendanceHead = NULL;
    }
};
class Student {
    Node_s* head=NULL;
public:
    //save student to file
    void saveToFile() {
        if (!head) {
            cout << "No students to save!" << endl;
            return;
        }
        ofstream student("AllStudent.txt", ios::trunc);
        Node_s* temp = head;
        while (temp!=NULL) {
            student << temp->ID << "\n" << temp->clas << "\n"
                << temp->phone << "\n" << temp->cnic << "\n"
                << temp->DoB << "\n" << temp->Name << "\n";
            temp = temp->next;
        }
        student.close();
        cout << "All students saved successfully." << endl;
    }
    // load Student from file
    void loadFile() {
        ifstream student("AllStudent.txt");
        if (!student.is_open()) {
            cout << "Error: Unable to open the file!" << endl;
            return;
        }

        string phone, cnic, dob, name;
        int id, clas;

        while (student >> id >> clas) {
            student.ignore();  // Ignore the newline after the class
            getline(student, phone);
            getline(student, cnic);
            getline(student, dob);
            getline(student, name);

            // Validate if all fields were successfully read
            if (student.fail()) {
                cout << "Error: Corrupted file format or insufficient data!" << endl;
                break;
            }

            InsertInOrder(cnic, phone, name, dob, id, clas);
        }

        student.close();
        cout << "Data successfully loaded from file!" << endl;
    }
    //Attendecse 
    void MarkAttendence() {// will mark attendence by searching id 
        Node_s* temp = head;
        AttendanceNode* current = temp->attendanceHead;
        string date, status;  
        
        /*int id;*/
        while (temp != NULL) {
        cout << "Enter the date of Attendense MM/DD/yy\n";
        cin >> date;
        cout << "Enter the date of Status (P/A)\n";
        cin >> status;
        /*cout << "Enter the id";
        cin >> id;*/
        AttendanceNode* newAttendence = new AttendanceNode(date,status);
       

          
        if (temp == NULL) {
            cout << "there is no student found for this id ";
        }
        else {
            newAttendence->next = temp->attendanceHead;//we are inseting attendence at starting node
            temp->attendanceHead = newAttendence;

        }
        temp = temp->next;
        }
    }
    void printAttendence() {
        Node_s* temp=head;

        int id;
       /* cout << "Enter the ID to know atendence history " << endl;
        cin >> id;*/
        while (temp!=NULL) {
            AttendanceNode* current = temp->attendanceHead;
            cout << "Date::  " << current->date << "Status::  " << current->status << endl;
           temp = temp->next;
        }
    }
    void Indviual() {
        int id=0;
        cout << "Enter the ID ";
        cin >> id;
        Node_s* temp = head;

        while (temp->ID != id && temp != NULL) {
            temp = temp->next;
        }
        if (temp != NULL) {
            int count = 0;
            AttendanceNode* current = temp->attendanceHead;
            while (current != NULL) {
                count++;
                cout << count << "\t" << current->status << endl;
                current = current->next;
            }
        }
    }
    //Adding student 
    void insertAtStart(string cn, string ph, string n, string dob, int i, int c) {
        
        Node_s* newStudent = new Node_s(cn, ph, n,dob, i, c);
        if (head == NULL) {
            head = newStudent;
        }
        else {
            newStudent->next = head;
            head->priv = newStudent;
            head = newStudent;
        }
    }
    void insertAtLast(string cn, string ph, string  n, string dob, int i, int c) {
        Node_s* newStudent = new Node_s(cn,ph,n, dob, i, c);
        Node_s* temp = head;
        while (temp->next != NULL) {
            temp = temp->next;
        }
        temp->next = newStudent;
        newStudent->priv = temp;
        
    }
    void InsertInOrder(string cn, string ph, string  n, string dob, int i, int c) {
        
        Node_s* newStudent = new Node_s(cn, ph, n, dob, i, c);
        Node_s* temp = head;

        // Traverse the list to find the correct insertion point
        while (temp != NULL && temp->ID < i) {
            temp = temp->next;
        }

        // If the list is empty or we are inserting at the beginning
        if (temp == head) {
            insertAtStart(cn, ph, n, dob, i, c);
        }
        // If we reached the end of the list
        else if (temp == NULL) {
            insertAtLast(cn, ph, n, dob, i, c);
        }
        // Inserting in the middle
        else {
            newStudent->next = temp;
            newStudent->priv = temp->priv;

            if (temp->priv != NULL) {  // Update the previous node's next pointer
                temp->priv->next = newStudent;
            }
            temp->priv = newStudent;
        }
    }
   //renoving student
    void deleteAtStart() {
        Node_s* temp = head;
        head = head->next;
        delete temp;
        temp = NULL;
        head->priv = NULL;
        saveToFile();
    }
    void removeAtLast() {
        Node_s* temp = head;
        while (temp->next != NULL) {
            temp = temp->next;
        }
        temp->priv->next = NULL;
        delete temp;
        temp = NULL;
        saveToFile();
    }
    void RemoveStudent() {
        int id;
        cout << "enter the id to delete student  " << endl;
        cin >>id;
        if (id == head->ID) {
            deleteAtStart();
            return;
        }
        else if(id < 1) {
            cout << "Invalid Entry " << endl;
            return;
        }
        else {
            Node_s* temp=head;
            while (temp->ID != id&&temp!=NULL) {
                temp = temp->next;
            }
            if (temp == NULL) {
                cout << "ID not found in recod " << endl;
                return;
            }
            else if (temp->next == NULL) {
                removeAtLast();
                return;
            }
            else {
                temp->priv->next = temp->next;
                temp->next->priv = temp->priv;
                delete  temp;
                temp = NULL;
                saveToFile();
            }
        }
    }
    //update the Student peronal details
    void update() {
        int id;
        cout << "Enter the ID  to update the details\n" << endl;
        cin >> id;
        Node_s* temp = head;
        while (temp != NULL) {
            if (id == temp->ID) {
                cout << "Enter the details \n";
                cout << "Enter the Name::";
                cin >> temp->Name;
                cout << "Enter the cnic::";
                cin >> temp->cnic;
                cout << "Enetr the phone number::";
                cin >> temp->phone;
                cout << "Enter the date of birth::";
                cin >> temp->DoB;
                cout << "Enter the id::";
                cin >> temp->ID;
                cout << "Enter the class::";
                cin >> temp->clas;
                saveToFile();
                return;
            }
            
            temp = temp->next;
        }
        
    }
    //searching student 
    void search() {
        int id;
        cout << "Enter the ID: ";
        cin >> id;
        Node_s* temp = head;
        while (temp != NULL) {
            if (temp->ID == id) {
                cout << "Name: " << temp->Name
                    << "\tID: " << temp->ID
                    << "\tPhone: " << temp->phone
                    << "\tDate of Birth: " << temp->DoB
                    << "\tCNIC: " << temp->cnic << endl;
                return;
            }
            temp = temp->next;
        }
        cout << "Not found\n";
    }
    //printing the whole list
    void print() {
        system("cls");
        Node_s* temp = head;
        cout << "=============================================================\n";
        while (temp != NULL) {
            cout << "Name\t" << temp->Name << "\t\t id\t" << temp->ID << "\t\tclass\t" << temp->clas << endl;
            temp = temp->next;
        }
        cout << "=============================================================\n";
    }
};
 
class Node_t {
public:
    int id;
    string name;
    long grade;
    string cnic;
    string phone;
    string email;
    Node_t* priv;
    Node_t* next;
    Node_t(string cn,string ph,string em,int ID, string n, long g) {
        cnic = cn;
        phone = ph;
        email = em;
        id = ID;
        name = n;
        grade = g;
        priv = NULL;
        next = NULL;
}
};
class Teacher {
    Node_t* head=NULL;
    public:
        //save Teachers to file
        void saveToFile() {
            if (!head) {
                cout << "No students to save!" << endl;
                return;
            }
            ofstream teacher("AllTeacher.txt", ios::trunc);
            Node_t* temp = head;
            while (temp) {
                teacher << temp->id << "\n" << temp->grade << "\n"
                    << temp->phone << "\n" << temp->cnic << "\n"
                    << temp->name <<"\n" << temp->email << "\n";
                temp = temp->next;
            }
            teacher.close();
            cout << "All teachers saved successfully." << endl;
        }
        // load Teachers from file
        void loadFile() {
            ifstream student("AllTeacher.txt");
            if (!student.is_open()) {
                cout << "Error: Unable to open the file!" << endl;
                return;
            }

            string phone, cnic, email, name;
            int id, grade;

            while (student >> id >> grade) {
                student.ignore();  // Ignore the newline after the class
                getline(student, phone);
                getline(student, cnic);
                getline(student, name);
                getline(student, email);

                // Validate if all fields were successfully read
                if (student.fail()) {
                    cout << "Error: Corrupted file format or insufficient data!" << endl;
                    break;
                }

                insertByOrder(cnic, phone, email, id, name,  grade);
            }

            student.close();
            cout << "Data successfully loaded from file!" << endl;
        }
        //inserting a teacher 
        void InsertAtStart(string cn, string ph, string em, int ID,string  n, long g) {
            Node_t* newteacher = new Node_t(cn,ph,em,ID, n, g);
            if (head == NULL) {
                head = newteacher;
            }
            else {
                newteacher->next = head;
                head->priv = newteacher;
                head = newteacher;
            }
        }
        void insertAtLast(string cn, string ph, string em, int ID, string  n, long g) {
            Node_t* newteacher = new Node_t(cn, ph, em,ID, n, g);
            Node_t* temp = head;
            while (temp->next != NULL) {
                temp = temp->next;
            }
            temp->next = newteacher;
            newteacher->priv = temp;
        }
        void insertByOrder(string cn, string ph, string em, int ID, string  n, long g) {
            system("cls");
            Node_t* newteacher = new Node_t(cn, ph, em, ID, n, g);
            Node_t* temp = head;

            // Traverse the list to find the correct insertion point
            while (temp != NULL && temp->id < ID) {
                temp = temp->next;
            }

            // If the list is empty or we are inserting at the beginning
            if (temp == head) {
                InsertAtStart(cn, ph, em, ID, n, g);
            }
            // If we reached the end of the list
            else if (temp == NULL) {
                insertAtLast(cn, ph, em, ID, n, g);
            }
            // Inserting in the middle
            else {
                newteacher->next = temp;
                newteacher->priv = temp->priv;

                if (temp->priv != NULL) {  // Update the previous node's next pointer
                    temp->priv->next = newteacher;
                }
                temp->priv = newteacher;
            }
        }
        //deleting the teacher
        void deleteAtStart() {
            if (head->next == NULL) {
                delete head;
                head = NULL;
            }
            else {
                head = head->next;
                delete head->priv;
                head->priv = NULL;
                saveToFile();

            }
        }
        void deleteAtLast() {
            Node_t* temp = head;
            while (temp->next != NULL) {
                temp = temp->next;
            }
            temp->priv->next = NULL;
            delete temp;
            temp = NULL;
            saveToFile();

        }
        void deleteByValue() {
            int value;
            cout << "enter the ID of Teacher to delete " << endl;
            cin >> value;
            Node_t* temp = head;
            if (value == head->id) {
                deleteAtStart();
            }
            else if (value < 1) {
                cout << "Invalid Entry " << endl;
                return;
            }
            else {
                Node_t* temp = head;
                while (temp->id != value && temp != NULL) {
                    temp = temp->next;
                }
                if (temp == NULL) {
                    cout << "ID not found in recod " << endl;
                }
                else if (temp->next == NULL) {
                    deleteAtLast();
                }
                else {
                    temp->priv->next = temp->next;
                    temp->next->priv = temp->priv;
                    delete  temp;
                    temp = NULL;
                    saveToFile();
                }
            }
        }
        //searching a teacher 
        void search() {
            int id;
            cout << "Enter the ID: ";
            cin >> id;
            Node_t* temp = head;
            while (temp != NULL) {
                if (temp->id == id) {
                    cout << "Name: " << temp->name
                        << "\tID: " << temp->id
                        << "\tPhone: " << temp->phone
                        << "\tDate of Birth: " << temp->email
                        << "\tAPay Scale : " << temp->grade
                        << "\tCNIC: " << temp->cnic << endl;
                    return;
                }
                temp = temp->next;
            }
            cout << "Not found\n";
        }
        // update the specific teacher details 
        void update() {
            int id;
            Node_t* temp = head;
            cout << "Enter the ID";
            cin >> id;
            while (temp != NULL) {
                if (id == temp->id) {
                    cout << "Enter the cnic";
                    cin >> temp->cnic;
                    cout << "Enter the Name";
                    cin >> temp->name;
                    cout << "ENter the the phone ";
                    cin >> temp->phone;
                    cout << "Enter the email";
                    cin >> temp->email;
                    cout << "Enetr the id ";
                    cin >> temp->id;
                    cout << "Ente the Grade";
                    cin >> temp->grade;
                    saveToFile();
                    return;
                }
            }
        }
        //printing the whole list
        void print() {
            Node_t* temp = head;

            while (temp != NULL) {
                cout << "Name\t" << temp->name << "\t\t id\t" << temp->id << "\t\tclass\t" << temp->grade << endl;
                temp = temp->next;
            }
            cout << "=============================================================\n";
        }
};



int login() {
    int choice;
    cout << "=============================================================\n";
    cout << "\t\tSCHOOL MANAGEMENT SYSTEM " << endl;
    cout << "=============================================================\n";
    cout << "Please select an option\n";
    cout << "1. Student\n";
    cout << "2. Teacher\n";
    cout << "3. Admin\n";
    cout << "=============================================================\n";
    cin >> choice;
    system("cls");
    return choice;
}

void manu() {

    Student s1;
    Teacher t1;
    s1.loadFile();
    t1.loadFile();
    string na, dob, cn, ph, em;
    int choice, id, c, g;
    int user = login();
    while (user < 4) {
        cout << "=============================================================\n";
        cout << "\t\tSCHOOL MANAGEMENT SYSTEM " << endl;
        cout << "=============================================================\n";
        if (user == 1) {
            while (true) {
                cout << "Please select an option" << endl;
                cout << "1. See your details in System \n";
                cout << "2. Update your detailis s\n";
                cout << "3. Check the attendence history of student\n";
                cout << "0.Exit \n";
                cout << "=============================================================\n";
                cout << "Enter you choice  ";
                cin >> choice;
                if (choice == 0) {
                    break;
                }
                else if (choice == 1) {
                    s1.search();
                }
                else if (choice == 2) {
                    s1.update();
                }
                else if (choice == 3) {
                    s1.printAttendence();
                }
            }
            
            user = login();
        }

        else if (user == 2) {
            while (true) {
                cout << "Please select an option" << endl;
                cout << "1. Search a Student in System \n";
                cout << "2. Mark the attendece\n";
                cout << "3. Check the attendence history of student\n";
                cout << "4. Cheack your details in System \n";
                cout << "5. Update the Your details\n";
                cout << "6. check indvidual attendence\n";
                cout << "0. Exit\n";
                cout << "=============================================================\n";
                cout << "Enter you choice  ";
                cin >> choice;
                if (choice < 1 || choice>7) {
                    break;
                }
                else if (choice == 1) {
                    s1.search();
                }
                else if (choice == 2) {
                    s1.MarkAttendence();
                }
                else if (choice == 3) {
                    s1.printAttendence();
                }
                else if (choice == 4) {
                    t1.search();
                }
                else if (choice == 5) {
                    t1.update();

                }
                else if (choice == 6) {
                    s1.Indviual();
                }

            }

            user = login();
        }

        else if (user == 3)  {
            while (true) {
                cout << "=============================================================\n";
                cout << "Please select an option" << endl;
                cout << "1. Enroll New Student \n";
                cout << "2. Search a Student in System \n";
                cout << "3. Update the student details\n";
                cout << "4. Check the attendence history of student\n";
                cout << "5. Enroll a Teacher\n";
                cout << "6. Search a Teacher in System \n";
                cout << "7. Update the Teacher details\n";
                cout << "8. Delete/remove the Student\n";
                cout << "9. Delete/remove the Student\n";
                cout << "10. Show all Student\n";
                cout << "11. Show all teacher\n";
                cout << "0. Exit\n";
                cout << "=============================================================\n";
                cout << "Enter you choice  ";
                cin >> choice;
                if (choice ==0) {
                    break;
                }
                else if (choice == 1) {
                    cout << "Enter the name of Student:: " ;
                    cin >> na;
                    cout << "Enter CNIC number::";
                    cin >> cn;
                    cout << "ENter thte Emergency number:: ";
                    cin >> ph;
                    cout << "Enter the Date of birth:: ";
                    cin >> dob;
                    cout << "Enter the ID::";
                    cin >> id;
                    cout << "Enter the class of Student::";
                    cin >> c;
                    s1.InsertInOrder(cn, ph, na, dob, id, c);
                    s1.saveToFile();
                   
                }
                else if (choice == 2) {
                    s1.search();
                }
                else if (choice == 3) {
                    s1.update();
                }
                else if (choice == 4) {
                    s1.printAttendence();
                }
                else if (choice == 5) {
                    cout << "Enter the name of Teacher " << endl;
                    cin >> na;
                    cout << "Enter CNIC number\n";
                    cin >> cn;
                    cout << "ENter thte Emergency number \n";
                    cin >> ph;
                    cout << "Enter the ID\n";
                    cin >> id;
                    cout << "Enter the Grade \n";
                    cin >> g;
                    cout << "ENter the Email\n";
                    cin >> em;
                    t1.insertByOrder(cn, ph, em, id, na, g); 
                    t1.saveToFile();
                }
                else if (choice == 6) {
                    t1.search();
                }
                else if (choice == 7) {
                    t1.update();
                }
                else if (choice == 8) {
                    s1.RemoveStudent();
                }
                else if (choice == 9) {
                    t1.deleteByValue();
                }
                else if (choice == 10) {
                    s1.print();
                }
                else if (choice == 11) {
                    t1.print();
                }
            }
            user = login();
        }
    }
}
int main()
{   
    manu();
}
