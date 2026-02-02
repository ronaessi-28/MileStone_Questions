Question 1>>>>
Here is the **deduplicated, cleaned, and consolidated version** of your requirement, with repeated information removed and everything organized clearly.

***

## Spring Boot Office Management System

### Departments & Employees

You are developing a **Spring Boot application** to manage **Departments** and their **Employees** in an office management system.

### Key Requirements

*   **Technology Stack**:  
    Spring Boot, Spring Data JPA, JPQL, RESTful APIs
*   **Architecture**:  
    Layered Architecture (Entity → Repository → Controller)
*   **Relationship**:
    *   One **Department** → Many **Employees**
    *   Each **Employee** belongs to only **one Department**

***

## Entity Layer

### Department Entity

**File:** `Department.java`

#### Variables

```java
private Long departmentId;
private String departmentName;
private String departmentDescription;
private String location;
private List<Employee> employees;
```

#### Description

*   Represents the **Department** entity.
*   Establishs a **One-to-Many** relationship with `Employee`.
*   One department can have multiple employees.
*   Relationship is mapped by the field name **`department`** in `Employee`.
*   Uses an appropriate **CascadeType**.
*   Uses `@JsonIgnoreProperties` to avoid infinite recursion during JSON serialization.
*   Includes **getters and setters** for all fields.

***

### Employee Entity

**File:** `Employee.java`

#### Variables

```java
private Long employeeId;
private String employeeName;
private String designation;
private String bio;
private Department department;
```

#### Description

*   Represents the **Employee** entity.
*   Establishs a **Many-to-One** relationship with `Department`.
*   Multiple employees can belong to one department.
*   Uses `@JoinColumn(name = "department_id")`.
*   Uses `@JsonIgnoreProperties` to avoid infinite recursion during JSON serialization.
*   Includes **getters and setters** for all fields.

***

## Repository Layer

### Department Repository

**File:** `DepartmentRepository.java`

#### Definition

```java
public interface DepartmentRepository extends JpaRepository<Department, Long>
```

#### Description

*   Provides CRUD operations for `Department`.
*   Includes a custom **JPQL-derived query** to fetch departments by name.

#### Method

```java
List<Department> findAllByDepartmentName(String departmentName);
```

***

## Controller Layer

### Department Controller

**File:** `DepartmentController.java`  
**Base URL:** `/departments`

#### Endpoints

1.  **Create Department**
    *   `POST /departments`
    *   Method:
        ```java
        public Department createDepartment(@RequestBody Department department)
        ```
    *   Saves and returns a department.

2.  **Get Department by ID**
    *   `GET /departments/{departmentId}`
    *   Method:
        ```java
        public Department getDepartment(@PathVariable Long departmentId)
        ```

3.  **Get Departments by Name**
    *   `GET /departments/getAllDepartments/{departmentName}`
    *   Method:
        ```java
        public List<Department> getAllDepartments(@PathVariable String departmentName)
        ```

4.  **Update Department**
    *   `PUT /departments/{departmentId}`
    *   Method:
        ```java
        public Department updateDepartment(@PathVariable Long departmentId,
                                           @RequestBody Department department)
        ```

***

### Employee Controller

**File:** `EmployeeController.java`  
**Base URL:** `/employees`

#### Endpoints

1.  **Create Employee**
    *   `POST /employees`
    *   Method:
        ```java
        public Employee createEmployee(@RequestBody Employee employee)
        ```

2.  **Get Employee by ID**
    *   `GET /employees/{employeeId}`
    *   Method:
        ```java
        public Employee getEmployee(@PathVariable Long employeeId)
        ```

3.  **Get All Employees**
    *   `GET /employees`
    *   Method:
        ```java
        public List<Employee> getAllEmployees()
        ```

4.  **Update Employee**
    *   `PUT /employees/{employeeId}`
    *   Method:
        ```java
        public Employee updateEmployee(@PathVariable Long employeeId,
                                       @RequestBody Employee employee)
        ```

***





**********************************************************************************Answer******************************************************************************************************

package com.edutech;

import java.util.ArrayList;
import java.util.List;

import javax.persistence.CascadeType;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;
import javax.persistence.OneToMany;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@SpringBootApplication
public class SpringBootAppApplication {

    public static void main(String[] args) {
        ConfigurableApplicationContext ctx=SpringApplication.run(SpringBootAppApplication.class, args);
        DepartmentController dpc=ctx.getBean(DepartmentController.class);
        EmployeeController epc=ctx.getBean(EmployeeController.class);
        //e
        Employee e=new Employee();
        e.setBio("hello");
        e.setDesignation("java");
        e.setEmployeeName("Viney");

        //e1
        Employee e1=new Employee();
        e1.setBio("jooooookkkkkk");
        e1.setDesignation("dotnet");
        e1.setEmployeeName("ayush");

        Department d=new Department();
        List<Employee> list1=new ArrayList<>();
         e.setDepartment(d);
         e1.setDepartment(d);
        d.setDepartmentDescription("hsjsj");
        d.setDepartmentName("kiiiii");
        d.setLocation("JIIIIIIIIIIII");
        list1.add(e1);
        list1.add(e);

        d.setEmployees(list1);
        dpc.createDepartment(d);
        



        
    }
}

@Entity
class Department{
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long departmentId;
    private String departmentName;
    private String departmentDescription;
    private String location;
    @JsonIgnoreProperties
    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL) 
    private List<Employee> employees;
    public Department() {
    }
    public Department(Long departmentId, String departmentName, String departmentDescription, String location,
            List<Employee> employees) {
        this.departmentId = departmentId;
        this.departmentName = departmentName;
        this.departmentDescription = departmentDescription;
        this.location = location;
        this.employees = employees;
    }
    public Department(String departmentName, String departmentDescription, String location, List<Employee> employees) {
        this.departmentName = departmentName;
        this.departmentDescription = departmentDescription;
        this.location = location;
        this.employees = employees;
    }
    public Long getDepartmentId() {
        return departmentId;
    }
    public void setDepartmentId(Long departmentId) {
        this.departmentId = departmentId;
    }
    public String getDepartmentName() {
        return departmentName;
    }
    public void setDepartmentName(String departmentName) {
        this.departmentName = departmentName;
    }
    public String getDepartmentDescription() {
        return departmentDescription;
    }
    public void setDepartmentDescription(String departmentDescription) {
        this.departmentDescription = departmentDescription;
    }
    public String getLocation() {
        return location;
    }
    public void setLocation(String location) {
        this.location = location;
    }
    public List<Employee> getEmployees() {
        return employees;
    }
    public void setEmployees(List<Employee> employees) {
        this.employees = employees;
    }
    
}
@Entity
class Employee{
        @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long employeeId;
    private String employeeName;
    private String designation;
    private String bio;

    @JsonIgnoreProperties
    @ManyToOne
    @JoinColumn(name = "department_id")
    private Department department;

    public Employee() {
    }

    public Employee(Long employeeId, String employeeName, String designation, String bio, Department department) {
        this.employeeId = employeeId;
        this.employeeName = employeeName;
        this.designation = designation;
        this.bio = bio;
        this.department = department;
    }

    public Employee(String employeeName, String designation, String bio, Department department) {
        this.employeeName = employeeName;
        this.designation = designation;
        this.bio = bio;
        this.department = department;
    }

    public Long getEmployeeId() {
        return employeeId;
    }

    public void setEmployeeId(Long employeeId) {
        this.employeeId = employeeId;
    }

    public String getEmployeeName() {
        return employeeName;
    }

    public void setEmployeeName(String employeeName) {
        this.employeeName = employeeName;
    }

    public String getDesignation() {
        return designation;
    }

    public void setDesignation(String designation) {
        this.designation = designation;
    }

    public String getBio() {
        return bio;
    }

    public void setBio(String bio) {
        this.bio = bio;
    }

    public Department getDepartment() {
        return department;
    }

    public void setDepartment(Department department) {
        this.department = department;
    } 
}

@Repository
interface DepartmentRepository extends JpaRepository<Department,Long>{
    @Query("select d from Department d where d.departmentName= :departmentName")
    List<Department> findAllByDepartmentName(@Param("departmentName") String DepartmentName);
}

@RestController
@RequestMapping("/departments")
class DepartmentController{


@Autowired
private DepartmentRepository departmentRepository;
@PostMapping
public Department createDepartment(@RequestBody Department department){
    return departmentRepository.save(department);
}

@GetMapping("/{departmentId}")
public Department getDepartment(@PathVariable Long departmentId){
    return departmentRepository.findById(departmentId).get();
}
@GetMapping("/getAllDepartments/{departmentName}")
public List<Department> getAllDepartments(@PathVariable String departmentName){
List<Department> d=departmentRepository.findAllByDepartmentName(departmentName);
return d;
}

@PutMapping("/{departmentId}")
public Department updateDepartment(@PathVariable Long departmentId, @RequestBody Department department){
    Department d=departmentRepository.findById(departmentId).get();
    d.setDepartmentDescription(department.getDepartmentDescription());
    d.setDepartmentName(department.getDepartmentName());
    d.setEmployees(department.getEmployees());
    d.setLocation(department.getLocation());
    return departmentRepository.save(d);
}

}

@Repository
interface EmployeeRepository extends JpaRepository<Employee,Long>{

}

@RestController
@RequestMapping("/employees")
class EmployeeController{
    @Autowired
    private EmployeeRepository employeeRepository;

    @PostMapping
    public Employee creatEmployee(@RequestBody Employee employee){
        return employeeRepository.save(employee);
    }
    @GetMapping("/{employeeId}")
    public Employee getEmployee(@PathVariable Long employeeId){
        return employeeRepository.findById(employeeId).get();
    }
    @GetMapping
    public List<Employee> getAllEmployees(){
        List<Employee> list=employeeRepository.findAll();
        return list;
    }
    @PutMapping("/{employeeId}")
    public Employee updateEmployee(@PathVariable Long employeeId, @RequestBody Employee employee){
        Employee emp=employeeRepository.findById(employeeId).get();
        emp.setBio(employee.getBio());
        emp.setDepartment(employee.getDepartment());
        emp.setDesignation(employee.getDesignation());
        emp.setEmployeeName(employee.getEmployeeName());
        return employeeRepository.save(emp);
    }



}
