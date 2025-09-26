# E-comm-website
package com.ecom_project.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
@CrossOrigion     //this will prevent the cors error  the both the frontend and backend 
public class ProductController {
     @Autowire
    private Service service;

    @GetMapping("/products")  //our url works as http://localhost:8080/api/products
    public ResponseEntity<List<Product>> getAllProducts(){
        return new ResponseEntity<>(service.getAllProducts(), HttpStatus.OK); //lets us also set the HTTP status code (like 200 OK, 201 CREATED, 404 NOT FOUND), headers, and the body of the response. This is crucial for building a proper REST API that can communicate success or failure to the client.
    }
@GetMapping("/products/{pathId}")
public ResponseEntity<Product> getProductById(@PathVariable int pathId) {
    Product product = service.getProductById(pathId);
    if (product != null) {
        return new ResponseEntity<>(productOptional.get(), HttpStatus.OK);
    } else {
        return new ResponseEntity<>(HttpStatus.NOT_FOUND);
    }
}
}package com.ecom_project.model;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.math.BigDecimal;
import java.util.Date;
@Entity            // this annotation is provided by hibernate orm mean the class name you provide is the table name  
@Data                 // these all three annotation is used to reduce the boiler plate using lombak dependency so we don have to create constructor of arg and non arg type and also their getter setter 
@AllArgsConstructor
@NoArgsConstructor
public class Product {
    @Id    //this will make id primary key
    @GeneratedValue(strategy = GenerationType.IDENTITY)  //TO AUTO GENERATE ID 1,2,...
private int id;
private String name;
private String desc;
private String brand;
private BitDecimal price;
private String category;
@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "dd-MM-yyyy") // JACKSON LIB annotation used to convert Java objects to JSON (serialization) and JSON to Java objects (deserialization).and also to enforce some data format like we are doing here as dd mm yyyy

private Date releaseDate;
private boolean available;
private int quantity;


}
package com.ecom_project.repository; 

import com.ecom_project.model.Product;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
@Repository
public interface ProductRepo extends JpaRepository<Product,Integer> {


}
package com.ecom_project.service;

import com.ecom_project.model.Product;
import com.ecom_project.repository.ProductRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.*;
@Service
import model.Product;
public class ProductService {
    @Autowired
    private ProductRepo repo;
public List<Product> getAllProducts(){
   return repo.findAll();   //directly fetch data from repo 
}
public Product getProductById(int prodId){
    return repo.findById(prodId)orElse(null);
}
}
spring.application.name=ecom-project
spring.datasource.url=jdbc:h2:mem:Db-ecom
spring.datasource.driverClassName = org.h2.Driver
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.defer-datasource-initialization = true   //"When we use an H2 database and run our project, Spring might try to connect to a table before JPA has finished creating it. This setting tells Spring to wait, so it only connects after the data and tables are fully loaded, preventing errors."
INSERT INTO product (id, name, desc, brand, price, category, release_date, available, quantity)
VALUES
(1, 'XUV500', 'A description of the XUV500', 'Mahindra', 15000.00, 'SUV', '2020-01-15', true, 5),
(2, 'Amaze', 'A description of the Amaze', 'Honda', 8500.50, 'Sedan', '2021-03-10', true, 8),
(3, 'Fortuner', 'A description of the Fortuner', 'Toyota', 35000.25, 'SUV', '2019-07-22', true, 3),
(4, 'Swift', 'A description of the Swift', 'Maruti Suzuki', 7500.00, 'Hatchback', '2022-09-05', true, 10),
(5, 'Creta', 'A description of the Creta', 'Hyundai', 12000.75, 'SUV', '2021-11-30', true, 6);          



    //todays controller service and model
    package com.ecom_project.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
@CrossOrigion     //this will prevent the cors error  the both the frontend and backend 
public class ProductController {
     @Autowire
    private Service service;

    @GetMapping("/products")  //our url works as http://localhost:8080/api/products
    public ResponseEntity<List<Product>> getAllProducts(){
        return new ResponseEntity<>(service.getAllProducts(), HttpStatus.OK); //lets us also set the HTTP status code (like 200 OK, 201 CREATED, 404 NOT FOUND), headers, and the body of the response. This is crucial for building a proper REST API that can communicate success or failure to the client.
    }
@GetMapping("/products/{pathId}")
public ResponseEntity<Product> getProductById(@PathVariable int pathId) {
    Product product = service.getProductById(pathId);
    if (product != null) {
        return new ResponseEntity<>(product, HttpStatus.OK);
    } else {
        return new ResponseEntity<>(HttpStatus.NOT_FOUND);
    }
}
@PostMapping("/product")
public ResponseEntity<?> addProduct(@RequestPart Product product,@RequestPart MultipartFile imageFile){
   try{ Product product1 = service.addProduct(product,imageFile);
    return new ResponseEntity<>(product1,HttpStatus.CREATED);

   }
   catch(Exception e){
    return new ResponseEntity<>(e.getMessage,HttpStatus.INTERNAL_SERVER_ERROR);

   }

}
@GetMapping("/product/{productId}/image")
public ResponseEntity<byte[]> getImageByProductId(@PathVariable int productId){
    Product product = service.getProductById(productId);
    byte[] imageFile = product.getImageData();
    return ResponseEntity.ok().contentType(MediaType.valueOf(product.getImageType()))
    .body(imageFile);

}
@PutMapping("/product/${prodId}")
public ResponseEntity<String> update(@PathVariable int prodId,@RequestPart Product product,@RequestPart MultipartFile imageFile){
    try{
        Product product = service.update(prodId, product, imageFile);
       
    }
    catch(Exception e){
         return new ResponseEntity<>("FAILED TO UPDATE" , HttpStatus.BAD_REQUEST);
    }
        if (product != null) {
        return new ResponseEntity<>("UPDATED", HttpStatus.OK);
    } else {
        return new ResponseEntity<>("FAILED TO UPDATE" , HttpStatus.BAD_REQUEST);
    }
}
     @DeleteMapping("/product/{id}")
       public ResponseEntity<String> delete(@PathVariable int pathId){
        Product product = service.update(prodId, product, imageFile);
          if (product != null) {
            return service.delete(id);
        return new ResponseEntity<>("deleted", HttpStatus.OK);
    } else {
        return new ResponseEntity<>("FAILED TO delete" , HttpStatus.BAD_REQUEST);
    }
       }
       @GetMapping("/products/search")
       public ResponseEntity<List<Product>> searchProducts( @RequestParam string keyword){
        System.out.println("searching with" + keyword);
        list<Product> products = service.searchProducts(keyword);
        return new ResponseEntity<>(products,HttpStatus OK);
       
       }
}
}



package com.ecom_project.model;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.math.BigDecimal;
import java.util.Date;
@Entity            // this annotation is provided by hibernate orm mean the class name you provide is the table name  
@Data                 // these all three annotation is used to reduce the boiler plate using lombak dependency so we don have to create constructor of arg and non arg type and also their getter setter 
@AllArgsConstructor
@NoArgsConstructor
public class Product {
    @Id    //this will make id primary key
    @GeneratedValue(strategy = GenerationType.IDENTITY)  //TO AUTO GENERATE ID 1,2,...
private int id;
private String name;
private String desc;
private String brand;
private BitDecimal price;
private String category;
@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "dd-MM-yyyy") // JACKSON LIB annotation used to convert Java objects to JSON (serialization) and JSON to Java objects (deserialization).and also to enforce some data format like we are doing here as dd mm yyyy

private Date releaseDate;
private boolean available;
private int quantity;
private String imageName;
private String imageType;
@Lob
private byte[] imageData;


}
package com.ecom_project.repository; 

import com.ecom_project.model.Product;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
@Repository
public interface ProductRepo extends JpaRepository<Product,Integer> {
@Query("SELECT p FROM Product p WHERE " +
   "LOWER(p.name) LIKE LOWER(CONCAT('%', :keyword, '%')) OR " +
   "LOWER(p.description) LIKE LOWER(CONCAT('%', :keyword, '%')) OR " +
   "LOWER(p.brand) LIKE LOWER(CONCAT('%', :keyword, '%')) OR " +
   "LOWER(p.category) LIKE LOWER(CONCAT('%', :keyword, '%'))")
List<Product> searchProducts(String keyword);

}



package com.ecom_project.service;

import com.ecom_project.model.Product;
import com.ecom_project.repository.ProductRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.*;
@Service
import model.Product;
public class ProductService {
    @Autowired
    private ProductRepo repo;
public List<Product> getAllProducts(){
   return repo.findAll();   //directly fetch data from repo 
}
public Product getProductById(int prodId){
    return repo.findById(prodId)orElse(null);
}
public Product addProduct(Product product , MultipartFile imageFile){ throws IOException
    product.setImageName(imageFile.getOrigionalFilename());
    product.setImageType(imageFile.getContentType());
    product.setImageDate(imageFile.getBytes());
   return repo.save(product);// to save to data
}
public Product update(int prodId, Product product,MultipartFile imageFile){
    product.setImageDate(imageFile.getBytes());    //these all are setting the data and image entered by user
    Product.setImageName(imageFile.getOrigionalFilename());
    Product.setImageType(imageFile.getContentType());
    return repo.save(product);
}

public void delete(int prodId){
      return repo.deleteById(id);
}
  public List<Product> searchProducts(  string keyword){
     return repo.searchProducts(keyword);
  }






FOR SPRING SECURITY 
sECURITYcONFIG.Data  under config package 
@Configuration
@EnableWebSecurity
public class SecurityConfig {
 @Bean
 public SecurityFilterChain SecurityFilterChain(HttpSecurity http) throws Exeption{
    http.csrf(customizer -> customizer.disable());
    http.authorizeHttpRequests(request -> request.anyRequest().authenticated());
    //http.formLogin(Customizer.withDefaults());
    http.httpBasic(Customizer.withDefaults());
    http.sessionManagement(session -> session.sessionCreationPolicy(sessionCreationPolicy.STATELESS));
    return http.build();
 }

}

  CLASSES controllers
  public class ProjectController {
    @RestController
    @RequestMapping("/")
    public String greet(HttpServletRequest request){
        System.out.print("hi this is a security project" + request.getSession().getId());
        }

}
public class Student {
   private int id;
private String name;
private int marks;
public  Student(int id, String name, int marks){
    this.id = id;
    this.name = name;
    this.marks = marks;
}

// Getter and Setter for id
public int getId() {
    return id;
}

public void setId(int id) {
    this.id = id;
}

// Getter and Setter for name
public String getName() {
    return name;
}

public void setName(String name) {
    this.name = name;
}

// Getter and Setter for marks
public int getMarks() {
    return marks;
}

public void setMarks(int marks) {
    this.marks = marks;
}
@Override
public String toString() {
    return "YourClassName{" +
           "id=" + id +
           ", name='" + name + '\'' +
           ", marks=" + marks +
           '}';


}
@RestController
public class StudentController {
  private List<Student> Students = new ArrayList<>(List.of(
         new Student(id:1, name:"kartik", marks:80),
         new Student(id:2, name:"Kiran", marks:86)
  ));
  @GetMapping("/students")
    public List<Student> getStudents(){
        return Students;
    }
     @GetMapping("/csrf-token")
    public CsrfToken getCsrfToken(HttpServletRequest request){
        return (CsrfToken) request.getAttribute("_csrf");
            
    }
@PostMapping("/students")
public Student add(@RequestBody Student student){
    return students.add(student);
}

}


  
