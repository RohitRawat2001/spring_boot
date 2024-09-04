# spring_boot

# spring_boot Project Architecture

![Screenshot 2024-09-04 105117](https://github.com/user-attachments/assets/b04fa635-9dc1-4c5f-a39c-2f43b776058c)



Spring Boot uses Hibernate Validator as the default implementation of the Bean Validation API (JSR 380). Here’s a list of commonly used bean validation annotations in Spring Boot:

# Common Bean Validation Annotations:
# @NotNull

Ensures that the annotated field is not null.
```java
Example: @NotNull private String name;
```
# @NotEmpty

Ensures that the annotated field is not null and the associated collection, map, or string is not empty.
```java
Example: @NotEmpty private List<String> items;
```
# @NotBlank

Ensures that the annotated string is not null and not empty after trimming.
```java
Example: @NotBlank private String username;
```
# @Size(min = ..., max = ...)

Validates that the size of the annotated element (collection, map, array, string) is within the specified bounds.
```java
Example: @Size(min = 2, max = 14) private String password;
```

# @Min(value = ...)

Ensures that the annotated element is a number whose value must be greater than or equal to the specified minimum.
```java
Example: @Min(18) private int age;
```

# @Max(value = ...)

Ensures that the annotated element is a number whose value must be less than or equal to the specified maximum.
```java
Example: @Max(65) private int retirementAge;
```

# @Positive

Ensures that the annotated number is positive (greater than 0).
```java
Example: @Positive private int quantity;
```

# @PositiveOrZero

Ensures that the annotated number is positive or zero.
```java
Example: @PositiveOrZero private int balance;
```

# @Negative

Ensures that the annotated number is negative (less than 0).
```java
Example: @Negative private int loss;
```

# @NegativeOrZero

Ensures that the annotated number is negative or zero.
```java
Example: @NegativeOrZero private int temperature;
```

# @Pattern(regexp = "...")

Validates that the annotated string matches the specified regular expression.
```java
Example: @Pattern(regexp = "^[a-zA-Z0-9]{6,12}$") private String username;
```

# @Email

Validates that the annotated string is a valid email address.
```java
Example: @Email private String emailAddress;
```
# @Past

Ensures that the annotated date or time is in the past.
```java
Example: @Past private LocalDate birthDate;
```

# @PastOrPresent

Ensures that the annotated date or time is in the past or present.
```java
Example: @PastOrPresent private LocalDateTime lastUpdated;
```

# @Future

Ensures that the annotated date or time is in the future.
```java
Example: @Future private LocalDate eventDate;
```

# @FutureOrPresent

Ensures that the annotated date or time is in the future or present.
```java
Example: @FutureOrPresent private LocalDateTime startTime;
```

# @AssertTrue

Ensures that the annotated boolean field is true.
```java
Example: @AssertTrue private boolean isActive;
```
# @AssertFalse

Ensures that the annotated boolean field is false.
```java
Example: @AssertFalse private boolean isDeleted;
```

# @Digits(integer = ..., fraction = ...)

Validates that the annotated number has an exact number of integer and fractional digits.
```java
Example: @Digits(integer = 5, fraction = 2) private BigDecimal price;
```

# @DecimalMin(value = "...")

Validates that the annotated element is a number whose value must be greater than or equal to the specified minimum and can handle BigDecimal.
```java
Example: @DecimalMin("0.01") private BigDecimal minimumPrice;
```

# @DecimalMax(value = "...")

Validates that the annotated element is a number whose value must be less than or equal to the specified maximum and can handle BigDecimal.
```java
Example: @DecimalMax("1000.00") private BigDecimal maximumPrice;
```

# @CreditCardNumber

Validates that the annotated string is a valid credit card number.
```java
Example: @CreditCardNumber private String creditCardNumber;
```

# @CNPJ (Brazilian)

Validates that the annotated string is a valid Brazilian CNPJ number.
```java
Example: @CNPJ private String companyCNPJ;
```

# @CPF (Brazilian)

Validates that the annotated string is a valid Brazilian CPF number.
```java
Example: @CPF private String individualCPF;
```
# Custom Validators:

# @Valid:

Used to validate nested objects. When applied to a field, the entire object graph is validated.
```java
Example: @Valid private Address address;
```

# @Valid List:

To validate a list of objects, use @Valid on the list itself.
```java
Example: @Valid private List<@Valid Address> addresses;
```
You can also create custom annotations by implementing the ConstraintValidator interface for more specific validation requirements.




# 1. Basic Example with Bean Validation Annotations:

```java
import jakarta.validation.constraints.*;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@SpringBootApplication
public class ValidationExampleApplication {
    public static void main(String[] args) {
        SpringApplication.run(ValidationExampleApplication.class, args);
    }
}

// User Entity with Validation Annotations
class User {

    @NotNull(message = "ID cannot be null")
    private Long id;

    @NotBlank(message = "Name cannot be blank")
    @Size(min = 2, max = 30, message = "Name must be between 2 and 30 characters")
    private String name;

    @Email(message = "Email should be valid")
    private String email;

    @Min(value = 18, message = "Age should not be less than 18")
    @Max(value = 100, message = "Age should not be more than 100")
    private int age;

    @Positive(message = "Salary must be positive")
    private double salary;

    // Getters and Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }

    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }

    public double getSalary() { return salary; }
    public void setSalary(double salary) { this.salary = salary; }
}

// REST Controller to Handle Validation
@RestController
@RequestMapping("/users")
class UserController {

    @PostMapping
    public String createUser(@RequestBody @Valid User user) {
        return "User created successfully!";
    }

    @PostMapping("/bulk")
    public String createUsers(@RequestBody @Valid List<@Valid User> users) {
        return "Users created successfully!";
    }
}

```

# 2. Example with Custom Validation Annotation:

To create a custom validation annotation, you’ll need to do the following:

# 1. Create the Annotation:

```java
import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Constraint(validatedBy = AdultAgeValidator.class)
@Target({ ElementType.FIELD, ElementType.PARAMETER })
@Retention(RetentionPolicy.RUNTIME)
public @interface AdultAge {
    String message() default "Age must be 18 or older";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
2. Create the Validator:
java
Copy code
import jakarta.validation.ConstraintValidator;
import jakarta.validation.ConstraintValidatorContext;

public class AdultAgeValidator implements ConstraintValidator<AdultAge, Integer> {

    @Override
    public boolean isValid(Integer value, ConstraintValidatorContext context) {
        return value != null && value >= 18;
    }
}
```

# 3. Use the Custom Validator in a Model:

```java
import jakarta.validation.constraints.NotBlank;

public class Person {

    @NotBlank(message = "Name cannot be blank")
    private String name;

    @AdultAge
    private int age;

    // Getters and Setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}
```

# 3. Testing Validation:
You can use the UserController to test validation:

If you send a POST request with invalid data (e.g., a blank name, an age less than 18), Spring Boot will automatically return a 400 Bad Request response with validation error messages.

For example, sending a request like this:
```json
{
  "name": "John",
  "email": "john.doe@example.com",
  "age": 15,
  "salary": -1000.00
}
```
will result in an error response:

```json
{
  "status": "BAD_REQUEST",
  "errors": [
    "Age should not be less than 18",
    "Salary must be positive"
  ]
}
```
This approach makes it easy to enforce data integrity rules across your application.
