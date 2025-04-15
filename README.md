[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/SPoPgs13)

Hibernate.cfg.xml

<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <property name="hibernate.connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/your_database</property>
        <property name="hibernate.connection.username">root</property>
        <property name="hibernate.connection.password">password</property>
        <property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
        <property name="hibernate.hbm2ddl.auto">update</property>
        <property name="show_sql">true</property>
        <mapping class="Student"/>
    </session-factory>
</hibernate-configuration>


Java code

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class HibernateCRUD {
    @Entity
    public static class Student {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private int id;
        private String name;
        private int age;

      
        public int getId() { return id; }
        public void setId(int id) { this.id = id; }
        public String getName() { return name; }
        public void setName(String name) { this.name = name; }
        public int getAge() { return age; }
        public void setAge(int age) { this.age = age; }
    }

    private static SessionFactory sessionFactory;

    static {
        try {
            sessionFactory = new Configuration()
                    .setProperty("hibernate.connection.driver_class", "com.mysql.cj.jdbc.Driver")
                    .setProperty("hibernate.connection.url", "jdbc:mysql://localhost:3306/your_database")
                    .setProperty("hibernate.connection.username", "root")
                    .setProperty("hibernate.connection.password", "password")
                    .setProperty("hibernate.dialect", "org.hibernate.dialect.MySQLDialect")
                    .setProperty("hibernate.hbm2ddl.auto", "update")
                    .setProperty("show_sql", "true")
                    .addAnnotatedClass(Student.class)
                    .buildSessionFactory();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        createStudent("John Doe", 20);
        readStudent(1);
        updateStudent(1, "Jane Doe", 25);
        deleteStudent(1);
    }

    public static void createStudent(String name, int age) {
        try (Session session = sessionFactory.openSession()) {
            Transaction tx = session.beginTransaction();
            Student student = new Student();
            student.setName(name);
            student.setAge(age);
            session.save(student);
            tx.commit();
            System.out.println("Student created: " + student.getId());
        }
    }

    public static void readStudent(int id) {
        try (Session session = sessionFactory.openSession()) {
            Student student = session.get(Student.class, id);
            if (student != null) {
                System.out.println("Student found: Name=" + student.getName() + ", Age=" + student.getAge());
            } else {
                System.out.println("Student not found!");
            }
        }
    }

    public static void updateStudent(int id, String name, int age) {
        try (Session session = sessionFactory.openSession()) {
            Transaction tx = session.beginTransaction();
            Student student = session.get(Student.class, id);
            if (student != null) {
                student.setName(name);
                student.setAge(age);
                session.update(student);
                System.out.println("Student updated: " + student.getId());
            } else {
                System.out.println("Student not found for update!");
            }
            tx.commit();
        }
    }

    public static void deleteStudent(int id) {
        try (Session session = sessionFactory.openSession()) {
            Transaction tx = session.beginTransaction();
            Student student = session.get(Student.class, id);
            if (student != null) {
                session.delete(student);
                System.out.println("Student deleted: " + id);
            } else {
                System.out.println("Student not found for deletion!");
            }
            tx.commit();
        }
    }
}
