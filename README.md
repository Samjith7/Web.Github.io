class CriticalConditionError(Exception):
    def __init__(self, message):
        super().__init__(message)
 
 
class ArrayStack:
    def __init__(self):
        self._data = []
 
    def push(self, item):
        self._data.append(item)
 
    def pop(self):
        if self._data:
            return self._data.pop()
        return None
 
    def peek(self):
        return self._data[-1] if self._data else None
 
    def __str__(self):
        return str(self._data)
 
 
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None
 
 
class LinkedListStack:
    def __init__(self):
        self.top = None
 
    def push(self, data):
        node = Node(data)
        node.next = self.top
        self.top = node
 
    def pop(self):
        if self.top:
            data = self.top.data
            self.top = self.top.next
            return data
        return None
 
    def peek(self):
        return self.top.data if self.top else None
 
    def display(self):
        result = []
        current = self.top
        while current:
            result.append(current.data)
            current = current.next
        return result
 
 
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age  = age
 
    def __str__(self):
        return f"{self.name} (Age: {self.age})"
 
    def generate_report(self):
        return f"Person: {self.name}"
 
 
class Patient(Person):
 
    hospital_name = "City Hospital"
 
    def __init__(self, name, age, patient_id, blood_group):
        super().__init__(name, age)
        self.patient_id     = patient_id
        self.blood_group    = blood_group
        self.temperature    = 98.6
        self.is_admitted    = False
        self.blood_pressure = 120 + 80j
        self.prescriptions  = []
        self.vitals_snapshot = ()
        self.allergies      = set()
        self.__medical_history = []
        self.prescription_stack = ArrayStack()
        self.consultation_stack = LinkedListStack()
 
    def show_id_conversions(self):
        print(f"  Patient ID (decimal) : {self.patient_id}")
        print(f"  Patient ID (binary)  : {bin(self.patient_id)}")
        print(f"  Patient ID (hex)     : {hex(self.patient_id)}")
 
    def set_temperature_from_string(self, temp_str):
        self.temperature = float(temp_str)
 
    def set_age_from_string(self, age_str):
        self.age = int(float(age_str))
 
    def compute_bmi(self, weight_kg, height_m):
        return weight_kg / (height_m ** 2)
 
    def compute_medicine_cost(self, cost_per_day, days):
        return cost_per_day * days
 
    def compute_insurance_claim(self, total_cost, rate=0.8):
        return total_cost * rate
 
    def encode_status(self, icu=False, oxygen=False, surgery=False):
        ICU_FLAG     = 0b001
        OXYGEN_FLAG  = 0b010
        SURGERY_FLAG = 0b100
        status = 0
        if icu:
            status = status | ICU_FLAG
        if oxygen:
            status = status | OXYGEN_FLAG
        if surgery:
            status = status | SURGERY_FLAG
        return status
 
    def decode_status(self, status):
        ICU_FLAG     = 0b001
        OXYGEN_FLAG  = 0b010
        SURGERY_FLAG = 0b100
        return {
            "ICU"    : bool(status & ICU_FLAG),
            "Oxygen" : bool(status & OXYGEN_FLAG),
            "Surgery": bool(status & SURGERY_FLAG),
        }
 
    def is_critical_care_needed(self, spo2):
        return (self.temperature > 103 or spo2 < 90) and self.age > 60
 
    def get_bmi_category(self, bmi):
        if bmi < 18.5:
            return "Underweight"
        elif bmi < 25.0:
            return "Normal"
        elif bmi < 30.0:
            return "Overweight"
        else:
            return "Obese"
 
    def assign_ward(self, is_critical, bmi):
        if is_critical:
            return "ICU"
        elif bmi > 30:
            return "Private"
        elif self.is_admitted:
            return "Semi-Private"
        else:
            return "General"
 
    def add_prescription(self, medicine):
        self.prescriptions.append(medicine)
        self.prescription_stack.push(medicine)
 
    def remove_prescription(self, medicine):
        if medicine in self.prescriptions:
            self.prescriptions.remove(medicine)
 
    def undo_last_prescription(self):
        removed = self.prescription_stack.pop()
        if removed and removed in self.prescriptions:
            self.prescriptions.remove(removed)
        return removed
 
    def get_sorted_prescriptions(self):
        return sorted(self.prescriptions)
 
    def get_weekly_vitals_grid(self):
        return [
            [round(self.temperature + day * 0.1 + hour * 0.05, 2) for hour in range(3)]
            for day in range(7)
        ]
 
    def set_vitals_snapshot(self, temp, bp_sys, bp_dia, spo2, pulse):
        self.vitals_snapshot = (temp, bp_sys, bp_dia, spo2, pulse)
 
    def unpack_vitals(self):
        temp, bp_sys, bp_dia, spo2, pulse = self.vitals_snapshot
        return temp, bp_sys, bp_dia, spo2, pulse
 
    def add_allergy(self, allergy):
        self.allergies.add(allergy)
 
    def remove_allergy(self, allergy):
        self.allergies.discard(allergy)
 
    def common_allergies_with(self, other_patient):
        return self.allergies & other_patient.allergies
 
    def all_allergies_with(self, other_patient):
        return self.allergies | other_patient.allergies
 
    def get_compatible_donors(self):
        match self.blood_group:
            case "A+":
                return ["A+", "A-", "O+", "O-"]
            case "A-":
                return ["A-", "O-"]
            case "B+":
                return ["B+", "B-", "O+", "O-"]
            case "B-":
                return ["B-", "O-"]
            case "AB+":
                return ["A+", "A-", "B+", "B-", "AB+", "AB-", "O+", "O-"]
            case "AB-":
                return ["A-", "B-", "AB-", "O-"]
            case "O+":
                return ["O+", "O-"]
            case "O-":
                return ["O-"]
            case _:
                return ["Unknown blood group"]
 
    def get_formatted_name(self):
        return self.name.strip().title()
 
    def get_patient_id_card(self):
        return f"PID-{str(self.patient_id).zfill(6)}"
 
    def get_prescription_text(self):
        return " | ".join(self.prescriptions)
 
    def parse_prescriptions_from_string(self, text):
        self.prescriptions = text.split(" | ")
 
    def validate_vitals(self, temp, spo2):
        if not (95 <= temp <= 107):
            raise ValueError(f"Invalid temperature: {temp}")
        if spo2 < 70:
            raise CriticalConditionError(f"SpO2 dangerously low: {spo2}")
        return True
 
    def save_vitals_to_file(self, spo2, pulse):
        filename = f"patient_{self.patient_id}_vitals.txt"
        with open(filename, "a") as f:
            f.write(f"Temp={self.temperature}, SpO2={spo2}, Pulse={pulse}\n")
 
    def read_vitals_history(self):
        filename = f"patient_{self.patient_id}_vitals.txt"
        try:
            with open(filename, "r") as f:
                return [line.strip() for line in f]
        except FileNotFoundError:
            return []
 
    def add_consultation(self, note):
        self.consultation_stack.push(note)
 
    def get_consultation_history(self):
        return self.consultation_stack.display()
 
    @property
    def medical_history(self):
        return self.__medical_history
 
    @medical_history.setter
    def medical_history(self, record):
        self.__medical_history.append(record)
 
    @classmethod
    def get_hospital_name(cls):
        return cls.hospital_name
 
    @staticmethod
    def is_valid_blood_group(bg):
        return bg in ["A+", "A-", "B+", "B-", "AB+", "AB-", "O+", "O-"]
 
    def __str__(self):
        return f"Patient: {self.name} | ID: {self.patient_id} | Blood: {self.blood_group}"
 
    def generate_report(self):
        return f"Patient Report → {self.name} | History: {self.__medical_history}"
 
 
class InPatient(Patient):
    def __init__(self, name, age, patient_id, blood_group, ward, days_admitted):
        super().__init__(name, age, patient_id, blood_group)
        self.ward          = ward
        self.days_admitted = days_admitted
        self.is_admitted   = True
 
    def get_long_stay_flag(self):
        return self.days_admitted > 5
 
    def __str__(self):
        return f"InPatient: {self.name} | Ward: {self.ward} | Days: {self.days_admitted}"
 
    def generate_report(self):
        return f"InPatient Report → {self.name} | Ward: {self.ward} | Days: {self.days_admitted}"
 
 
class OutPatient(Patient):
    def __init__(self, name, age, patient_id, blood_group, appointment_date):
        super().__init__(name, age, patient_id, blood_group)
        self.appointment_date = appointment_date
 
    def __str__(self):
        return f"OutPatient: {self.name} | Appointment: {self.appointment_date}"
 
    def generate_report(self):
        return f"OutPatient Report → {self.name} | Appointment: {self.appointment_date}"
 
 
class Doctor(Person):
    def __init__(self, name, age, specialization):
        super().__init__(name, age)
        self.specialization = specialization
 
    def __str__(self):
        return f"Dr. {self.name} | Specialization: {self.specialization}"
 
    def generate_report(self):
        return f"Doctor Report → Dr. {self.name} | Specialization: {self.specialization}"
 
 
class WardIterator:
    def __init__(self, patients):
        wards = {}
        for p in patients:
            w = getattr(p, "ward", "General")
            wards.setdefault(w, []).append(p.name)
        self.ward_list = list(wards.items())
        self.index = 0
 
    def __iter__(self):
        return self
 
    def __next__(self):
        if self.index >= len(self.ward_list):
            raise StopIteration
        ward_name, names = self.ward_list[self.index]
        self.index += 1
        return ward_name, names
 
 
class Hospital:
    def __init__(self, name):
        self.name            = name
        self.patient_records = {}
 
    def admit_patient(self, patient):
        self.patient_records[patient.patient_id] = patient
 
    def get_patient(self, patient_id):
        return self.patient_records.get(patient_id, None)
 
    def get_all_patients(self):
        return list(self.patient_records.values())
 
    def get_long_stay_patients(self):
        return [p.name for p in self.patient_records.values()
                if isinstance(p, InPatient) and p.days_admitted > 5]
 
    def get_critical_summary(self):
        return {pid: p.name
                for pid, p in self.patient_records.items()
                if p.is_critical_care_needed(spo2=88)}
 
    def critical_alerts(self):
        for patient in self.patient_records.values():
            if patient.is_critical_care_needed(spo2=88):
                yield patient.name
 
    def find_first_available_icu(self, ward_rooms):
        for room, status in ward_rooms.items():
            if status == "empty":
                return room
            else:
                continue
        return None
 
    def monitor_vitals_until_stable(self, readings):
        results = []
        index = 0
        while index < len(readings):
            reading = readings[index]
            if reading <= 99:
                results.append(f"{reading}°F → Stable")
                break
            results.append(f"{reading}°F → Elevated")
            index += 1
        return results
 
    def show_ward_wise_patients(self):
        patients = [p for p in self.patient_records.values() if isinstance(p, InPatient)]
        for ward_name, names in WardIterator(patients):
            print(f"  Ward: {ward_name} → {names}")
 
 
if __name__ == "__main__":
 
    print("=" * 60)
    print("       HOSPITAL PATIENT RECORD SYSTEM")
    print("=" * 60)
 
    hospital = Hospital("City Hospital")
 
    p1  = InPatient("Arjun Sharma", 65, 0o721, "A+", "ICU", 7)
    p2  = InPatient("Priya Singh",  35, 1001,  "O+", "General", 3)
    p3  = OutPatient("Rahul Verma", 45, 1002,  "B+", "2026-05-20")
    doc = Doctor("Dr. Mehta", 50, "Cardiology")
 
    hospital.admit_patient(p1)
    hospital.admit_patient(p2)
    hospital.admit_patient(p3)
 
    print("\n--- Section 1: Data Types & Literals ---")
    print(f"  Patient ID (octal literal 0o721) : {p1.patient_id}")
    print(f"  Temperature (float)              : {p1.temperature}")
    print(f"  Is Admitted (bool)               : {p1.is_admitted}")
    print(f"  Blood Pressure (complex)         : {p1.blood_pressure.real}/{p1.blood_pressure.imag}")
 
    print("\n--- Section 2: Base & Type Conversions ---")
    p1.show_id_conversions()
    p1.set_temperature_from_string("104.2")
    print(f"  Temperature from string: {p1.temperature}")
 
    print("\n--- Section 3: Arithmetic & Bitwise ---")
    bmi   = p1.compute_bmi(70, 1.75)
    cost  = p1.compute_medicine_cost(150, 7)
    claim = p1.compute_insurance_claim(cost)
    print(f"  BMI              : {bmi:.2f}")
    print(f"  Medicine Cost    : ₹{cost}")
    print(f"  Insurance Claim  : ₹{claim}")
    status_code = p1.encode_status(icu=True, oxygen=True)
    print(f"  Encoded Status   : {bin(status_code)}")
    print(f"  Decoded Status   : {p1.decode_status(status_code)}")
 
    print("\n--- Section 4: Critical Care Check ---")
    print(f"  {p1.name} needs critical care: {p1.is_critical_care_needed(spo2=88)}")
 
    print("\n--- Section 5: BMI Category & Ward ---")
    category = p1.get_bmi_category(bmi)
    ward     = p1.assign_ward(is_critical=True, bmi=bmi)
    print(f"  BMI Category : {category}")
    print(f"  Ward Assigned: {ward}")
 
    print("\n--- Section 6: Prescriptions List ---")
    p1.add_prescription("Paracetamol 500mg")
    p1.add_prescription("Ibuprofen 400mg")
    p1.add_prescription("Metformin 500mg")
    print(f"  Prescriptions   : {p1.prescriptions}")
    print(f"  Sorted          : {p1.get_sorted_prescriptions()}")
    print(f"  Undo last entry : {p1.undo_last_prescription()}")
    print(f"  After undo      : {p1.prescriptions}")
    print(f"\n  Long-stay patients: {hospital.get_long_stay_patients()}")
    grid = p1.get_weekly_vitals_grid()
    print(f"\n  Weekly Vitals Grid (Day 1): {grid[0]}")
 
    print("\n--- Section 7: Vitals Snapshot (Tuple) ---")
    p1.set_vitals_snapshot(104.2, 120, 80, 88, 72)
    t, bp_s, bp_d, s, pulse = p1.unpack_vitals()
    print(f"  Temp={t}, BP={bp_s}/{bp_d}, SpO2={s}, Pulse={pulse}")
 
    print("\n--- Section 8: Allergies (Set) ---")
    p1.add_allergy("Penicillin")
    p1.add_allergy("Aspirin")
    p1.add_allergy("Sulfa")
    p1.remove_allergy("Sulfa")
    p2.add_allergy("Aspirin")
    p2.add_allergy("Latex")
    print(f"  {p1.name}'s Allergies : {p1.allergies}")
    print(f"  Common with {p2.name}: {p1.common_allergies_with(p2)}")
    print(f"  All Allergies        : {p1.all_allergies_with(p2)}")
 
    print("\n--- Section 9: Patient Records (Dictionary) ---")
    print(f"  All Patient IDs : {list(hospital.patient_records.keys())}")
    print(f"  Critical Summary: {hospital.get_critical_summary()}")
 
    print("\n--- Section 10: Loops ---")
    ward_rooms = {"ICU-1": "occupied", "ICU-2": "empty", "ICU-3": "occupied"}
    print(f"  First ICU room  : {hospital.find_first_available_icu(ward_rooms)}")
    vitals_log = hospital.monitor_vitals_until_stable([104.2, 103.0, 101.5, 98.6])
    print(f"  Vitals monitoring:")
    for v in vitals_log:
        print(f"    {v}")
 
    print("\n--- Section 11: Blood Group Compatibility ---")
    for patient in [p1, p2, p3]:
        print(f"  {patient.name} ({patient.blood_group}) ← {patient.get_compatible_donors()}")
 
    print("\n--- Section 12: String Methods ---")
    print(f"  Formatted Name : {p1.get_formatted_name()}")
    print(f"  ID Card        : {p1.get_patient_id_card()}")
    print(f"  Prescriptions  : {p1.get_prescription_text()}")
 
    print("\n--- Section 13: Exception Handling ---")
    for temp, spo2 in [(98.6, 97), (200.0, 97), (99.0, 65)]:
        try:
            p1.validate_vitals(temp, spo2)
            print(f"  Vitals OK → Temp={temp}, SpO2={spo2}")
        except ValueError as e:
            print(f"  ValueError            : {e}")
        except CriticalConditionError as e:
            print(f"  CriticalConditionError: {e}")
 
    print("\n--- Section 14: File Handling ---")
    p1.save_vitals_to_file(spo2=88, pulse=72)
    p1.save_vitals_to_file(spo2=91, pulse=75)
    history = p1.read_vitals_history()
    print(f"  Vitals History for {p1.name}:")
    for h in history:
        print(f"    {h}")
 
    print("\n--- Section 15: Iterators & Generators ---")
    print("  Ward-wise Patients:")
    hospital.show_ward_wise_patients()
    print("  Critical Alerts:")
    for alert in hospital.critical_alerts():
        print(f"    ALERT: {alert} needs urgent attention!")
 
    print("\n--- Section 16: OOP ---")
    p1.medical_history = "Hypertension"
    p1.medical_history = "Diabetes Type 2"
    print(f"  {p1}")
    print(f"  {p2}")
    print(f"  {p3}")
    print(f"  {doc}")
    print(f"\n  Polymorphism via generate_report():")
    for entity in [p1, p2, p3, doc]:
        print(f"    {entity.generate_report()}")
    print(f"\n  Hospital (classmethod)    : {Patient.get_hospital_name()}")
    print(f"  Valid 'AB+' (staticmethod): {Patient.is_valid_blood_group('AB+')}")
    print(f"  Medical History (property): {p1.medical_history}")
 
    print("\n--- Section 17: Stacks ---")
    print(f"  Prescription Stack (array)       : {p1.prescription_stack}")
    p1.add_consultation("2026-04-01: General Checkup")
    p1.add_consultation("2026-04-15: Blood Test")
    p1.add_consultation("2026-05-01: Cardiology Review")
    print(f"  Consultation History (LinkedList): {p1.get_consultation_history()}")
    print(f"  Latest Consultation              : {p1.consultation_stack.peek()}")
 
    print("\n" + "=" * 60)
    print("  All sections demonstrated successfully!")
    print("=" * 60)
 
