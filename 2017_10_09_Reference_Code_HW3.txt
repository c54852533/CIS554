//HW3
//Due: 11:59PM, September 6 (Friday)
//By C-Y (Roger) Chen
#include <iostream>
#include <list>
#include <map>
#include <string>
using namespace std;

class course {
public:
	string name;
	int section;
	int credits;
	course() {}
	course(string n, int s, int c) { name = n; section = s; credits = c; }
	bool operator<(course c) { return (name < c.name); }
	bool operator==(course c) { return (name == c.name); }
};
void add_student(map<int, map<int, list<course> * >> &DB, int id);
void remove_student(map<int, map<int, list<course> * >> &DB, int id);
void add_course(map<int, map<int, list<course> * >> &DB, int semester, int id, course c); //20171 Spring semester of 2017; 20172: Fall semester of 2017
																						  //All courses in the list should be sorted according to name (increasing order)
void drop_course(map<int, map<int, list<course> * >> &DB, int semester, int id, course c);
void print_student_semester_courses(map<int, map<int, list<course> * >> &DB, int semester, int id);
void print_student_all_courses(map<int, map<int, list<course> * >> &DB, int id);
void print_DB(map<int, map<int, list<course> * >> &DB);

int main() {
	map<int, map<int, list<course> *>> DB;
	add_student(DB, 11111);
	course C1("CIS554", 1, 3), C2("CSE674", 1, 3), C3("MAT296", 8, 4), C4("WRT205", 5, 3);

	add_course(DB, 20171, 11111, C1);
	add_course(DB, 20171, 11111, C4);
	add_course(DB, 20171, 11111, C3);
	add_course(DB, 20171, 11111, C2);
	print_student_semester_courses(DB, 20171, 11111);

	drop_course(DB, 20171, 11111, C1);
	print_student_semester_courses(DB, 20171, 11111);

	add_course(DB, 20172, 11111, C2);
	add_course(DB, 20172, 11111, C4);
	add_course(DB, 20172, 11111, C3);
	add_course(DB, 20172, 11111, C1);
	print_student_all_courses(DB, 11111);

	add_student(DB, 11112);
	add_course(DB, 20171, 11112, C2);
	add_course(DB, 20171, 11112, C4);
	add_course(DB, 20171, 11112, C3);
	add_course(DB, 20171, 11112, C1);
	print_student_semester_courses(DB, 20171, 11112);

	add_course(DB, 20172, 11112, C2);
	add_course(DB, 20172, 11112, C4);
	add_course(DB, 20172, 11112, C3);
	add_course(DB, 20172, 11112, C1);
	print_student_semester_courses(DB, 20172, 11112);

	print_student_all_courses(DB, 11112);

	print_DB(DB);
	remove_student(DB, 11111);
	print_DB(DB);
	getchar();
	getchar();
	return 0;
}

void add_student(map<int, map<int, list<course> * >> &DB, int id) {
	if (DB.find(id) != DB.end()) return;
	map<int, list<course> *> M1;
	DB[id] = M1;
}
void remove_student(map<int, map<int, list<course> * >> &DB, int id) {
	if (DB.size() == 0) return;
	if (DB.find(id) == DB.end()) return;
	if (DB[id].size() == 0) return;
	map<int, list<course> *>::iterator it1 = DB[id].begin();
	while (it1 != DB[id].end()) {
		delete(it1->second);
		it1++;
	}
	DB.erase(id);
}


void add_course(map<int, map<int, list<course> * >> &DB, int semester, int id, course c) {

	if (DB.find(id) == DB.end()) return;

	if (DB[id].size() == 0 || DB[id].find(semester) == DB[id].end()) {
		DB[id][semester] = new list<course>;
		DB[id][semester]->push_back(c);
		return;
	}

	else {

		if (find(DB[id][semester]->begin(), DB[id][semester]->end(), c) != DB[id][semester]->end()) return;
		DB[id][semester]->push_front(c);
		DB[id][semester]->sort();
	}
}

void drop_course(map<int, map<int, list<course> * >> &DB, int semester, int id, course c) {

	if (DB.find(id) == DB.end()) return;
	if (DB[id].size() == 0) return;
	if (DB[id].find(semester) == DB[id].end()) return;
	DB[id][semester]->remove(c);
}

void print_student_semester_courses(map<int, map<int, list<course> * >> &DB, int semester, int id) {
	cout << endl;
	if (DB.size() == 0) return;
	if (DB.find(id) == DB.end()) return;
	if (DB[id].find(semester) == DB[id].end()) return;
	cout << "student id = " << id << endl;
	if (DB[id][semester]->size() != 0) {
		cout << "semester = " << semester << endl;
		list<course>::iterator it1 = DB[id][semester]->begin();
		while (it1 != DB[id][semester]->end()) {
			cout << it1->name << " " << it1->section << " " << it1->credits << "  ";
			it1++;
		}
		cout << endl;
	}
}
void print_student_all_courses(map<int, map<int, list<course> * >> &DB, int id) {
	cout << endl;
	if (DB.size() == 0) return;
	if (DB.find(id) == DB.end()) return;
	map<int, list<course> *>::iterator it1 = DB[id].begin();
	cout << "student id = " << id << endl;
	while (it1 != DB[id].end()) {

		list<course>::iterator it2 = it1->second->begin();
		cout << "semester = " << it1->first << endl; ;
		while (it2 != it1->second->end()) {
			cout << it2->name << " " << it2->section << " " << it2->credits << "  ";
			it2++;
		}
		cout << endl;
		it1++;
	}
}

void print_DB(map<int, map<int, list<course> * >> &DB) {
	cout << endl;
	if (DB.size() == 0) return;
	map<int, map<int, list<course> *> >::iterator it1 = DB.begin();

	while (it1 != DB.end()) {
		if (it1->second.size() != 0) {
			map<int, list<course> *>::iterator it2 = it1->second.begin();
			cout << "student id = " << it1->first << endl;
			while (it2 != it1->second.end()) {
				if (it2->second->size() != 0) {
					cout << "semester = " << it2->first << endl;
					list<course>::iterator it3 = it2->second->begin();
					while (it3 != it2->second->end()) {
						cout << it3->name << " " << it3->section << " " << it3->credits << "  ";
						it3++;
					}
					cout << endl;
				}
				it2++;
			}
		}
		it1++;
	}
}

