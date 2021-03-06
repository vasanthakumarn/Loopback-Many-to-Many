# Loopback-Many-to-Many
Many to many relationship Through relations

# Loopback Application

The project is generated by [LoopBack](http://loopback.io).

# Overview
A hasManyThrough relation sets up a many-to-many connection with another model. This relation indicates that the declaring model can be matched with zero or more instances of another model by proceeding through a third model. For example, in an application for a medical practice where patients make appointments to see physicians, the relevant relation declarations might be:


The “through” model, Appointment, has two foreign key properties, physicianId and patientId, that reference the primary keys in the declaring model, Physician, and the target model, Patient.

Defining a hasManyThrough relation
Use apic loopback:relation to create a relation between two models. The tool will prompt you to enter the name of the model, the name of related model, and other required information. The tool will then modify the model definition JSON file (for example, common/models/customer.json) accordingly.

Use slc loopback:relation to create a relation between two models. The tool will prompt you to enter the name of the model, the name of related model, and other required information. The tool will then modify the model definition JSON file (for example, common/models/customer.json) accordingly.

For more information, see Relation generator.

To create a hasManyThrough relation, respond with Yes to the prompt for a “through” model, then specify the model:

[?] Require a through model? Yes
[?] Choose a through model: Appointment
For example:

#  common/models/physician.json
```{  
  "name": "Physician",
  "base": "PersistedModel",
  "properties": {
    "name": {
      "type": "string"
    }
  },
  "validations": [],
  "relations": {
    "patients": {
      "type": "hasMany",
      "model": "Patient",
      "foreignKey": "physicianId",
      "through": "Appointment"
    },
```

# common/models/patient.json
```
{  
  "name": "Patient",
  "base": "PersistedModel",
  "properties": {
    "name": {
      "type": "string"
    }
  },
  "validations": [],
  "relations": {
    "physicans": {
      "type": "hasMany",
      "model": "Physician",
      "foreignKey": "patientId",
      "through": "Appointment"
    },...
```

# common/models/appointment.json
```{  
  "name": "Appointment",
  "base": "PersistedModel",
  "properties": {
    "appointmentDate": {
      "type": "date"
    }
  },
  "validations": [],
  "relations": {
    "physician": {
      "type": "belongsTo",
      "model": "Physician",
      "foreignKey": "physicianId"
    },
    "patient": {
      "type": "belongsTo",
      "model": "Patient",
      "foreignKey": "patientId"
    }
```

# Create the physicians via 
```
POST : http://localhost:3000/api/physicians
Body : [{"key":"name","value":"doctor1"}]
Result : {
    "name": "doctor1",
    "id": 1
}
Body : [{"key":"name","value":"doctor2"}]
Result : {
    "name": "doctor2",
    "id": 2
}
```
# Create the patient via 
```
POST : http://localhost:3000/api/patients
BODY : [{"key":"name","value":"p1"}]
RESULT : {
    "name": "p1",
    "id": 1
}
BODY : [{"key":"name","value":"p2"}]
RESULT : {
    "name": "p2",
    "id": 2
}
```

#  Create the appointment via 
```
POST : http://localhost:3000/api/appointments
BODY : [{"key":"patientId","value":"1"},{"key":"physicianId","value":"1"},{"key":"appointmentDate","value":"2017-06-01"}]
RESULT : {
    "appointmentDate": "2017-06-01T00:00:00.000Z",
    "id": 1,
    "physicianId": 1,
    "patientId": 1
}

BODY : [{"key":"patientId","value":"2"},{"key":"physicianId","value":"1"},{"key":"appointmentDate","value":"2017-06-01"}]
RESULT : {
    "appointmentDate": "2017-06-01T00:00:00.000Z",
    "id": 2,
    "physicianId": 1,
    "patientId": 2
}
```

#  Getting Many to Many result via 
```
GET : http://localhost:3000/api/appointments?filter={"include":["patient","physician"],"where":{"physicianId":1}}
RESULT : [
    {
        "appointmentDate": "2017-06-01T00:00:00.000Z",
        "id": 1,
        "physicianId": 1,
        "patientId": 1,
        "patient": {
            "name": "p1",
            "id": 1
        },
        "physician": {
            "name": "doctor1",
            "id": 1
        }
    },
    {
        "appointmentDate": "2017-06-01T00:00:00.000Z",
        "id": 2,
        "physicianId": 1,
        "patientId": 2,
        "patient": {
            "name": "p2",
            "id": 2
        },
        "physician": {
            "name": "doctor1",
            "id": 1
        }
    }
]
```




