# Adding Email Validation and Duplication Check in OAuth Login with Spring Boot

In modern web applications, user authentication is a critical feature. Using OAuth with frameworks like Spring Boot makes it easy to integrate third-party providers like Google for authentication. However, ensuring data validity and uniqueness, especially for emails, is essential for a robust system.

This article demonstrates how to implement email validation, duplication checks, and user information handling in an OAuth-based application using Spring Boot and React.

---

## **Setup Overview**

Before diving into validation, let’s ensure you have the following setup:

1. **Spring Boot with OAuth 2.0**
2. **React frontend**
3. **User entity with fields for email, name, and picture**

If your base project is ready, we’ll now enhance the service logic to include:
- Email format validation
- Duplicate email checks
- Name length checks
- Image URL validation

---

## **Service Logic**

The service layer will handle all the validation logic. Here is the complete implementation:

### **`UserService.java`**

```java
@Service
@Transactional
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    private static final String EMAIL_REGEX = "^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,6}$";
    private static final int MAX_NAME_LENGTH = 50;
    private static final int MAX_IMAGE_URL_LENGTH = 200;
    private static final String IMAGE_URL_REGEX = "^(http|https)://.*\\.(jpg|jpeg|png|gif)$";

    public User saveOrUpdateUser(String email, String name, String picture) {
        // Validate inputs
        validateEmail(email);
        validateName(name);
        validateImageUrl(picture);

        // Check for duplicate emails
        checkEmailDuplicate(email);

        // Save or update user
        User existingUser = userRepository.findByEmail(email);
        if (existingUser != null) {
            existingUser.setName(name);
            existingUser.setPicture(picture);
            return userRepository.save(existingUser);
        }

        User newUser = new User();
        newUser.setEmail(email);
        newUser.setName(name);
        newUser.setPicture(picture);
        return userRepository.save(newUser);
    }

    private void validateEmail(String email) {
        if (email == null || email.isEmpty()) {
            throw new CustomException("INVALID_EMAIL", "Email cannot be null or empty.");
        }
        if (!Pattern.matches(EMAIL_REGEX, email)) {
            throw new CustomException("INVALID_EMAIL_FORMAT", "Invalid email format: " + email);
        }
    }

    private void validateName(String name) {
        if (name == null || name.isEmpty()) {
            throw new CustomException("INVALID_NAME", "Name cannot be null or empty.");
        }
        if (name.length() > MAX_NAME_LENGTH) {
            throw new CustomException("INVALID_NAME_LENGTH", "Name is too long. Maximum allowed length is " + MAX_NAME_LENGTH + " characters.");
        }
    }

    private void validateImageUrl(String picture) {
        if (picture == null || picture.isEmpty()) {
            throw new CustomException("INVALID_IMAGE_URL", "Image URL cannot be null or empty.");
        }
        if (picture.length() > MAX_IMAGE_URL_LENGTH) {
            throw new CustomException("INVALID_IMAGE_URL_LENGTH", "Image URL is too long. Maximum allowed length is " + MAX_IMAGE_URL_LENGTH + " characters.");
        }
        if (!Pattern.matches(IMAGE_URL_REGEX, picture)) {
            throw new CustomException("INVALID_IMAGE_FORMAT", "Invalid image URL format: " + picture);
        }
    }

    private void checkEmailDuplicate(String email) {
        if (userRepository.findByEmail(email) != null) {
            throw new CustomException("DUPLICATE_EMAIL", "The email is already in use: " + email);
        }
    }
}
```

---

## **Exception Handling**

To ensure errors are communicated clearly to the frontend, we’ll use a global exception handler.

### **`CustomException.java`**

```java
public class CustomException extends RuntimeException {
    private final String errorCode;
    private final String errorMessage;

    public CustomException(String errorCode, String errorMessage) {
        super(errorMessage);
        this.errorCode = errorCode;
        this.errorMessage = errorMessage;
    }

    public String getErrorCode() {
        return errorCode;
    }

    public String getErrorMessage() {
        return errorMessage;
    }
}
```

### **`GlobalExceptionHandler.java`**

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(CustomException.class)
    public ResponseEntity<Map<String, String>> handleCustomException(CustomException ex) {
        Map<String, String> response = new HashMap<>();
        response.put("errorCode", ex.getErrorCode());
        response.put("errorMessage", ex.getErrorMessage());
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(response);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<Map<String, String>> handleGeneralException(Exception ex) {
        Map<String, String> response = new HashMap<>();
        response.put("errorCode", "INTERNAL_SERVER_ERROR");
        response.put("errorMessage", "An unexpected error occurred.");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(response);
    }
}
```

---

## **Frontend Integration**

In the React frontend, error messages from the backend are displayed to the user.

### **React Error Handling**

#### **`App.js`**

```javascript
import React, { useState, useEffect } from 'react';
import axios from 'axios';

const App = () => {
    const [user, setUser] = useState(null);
    const [error, setError] = useState(null);

    useEffect(() => {
        axios.get('/api/user')
            .then(response => setUser(response.data))
            .catch(error => {
                if (error.response) {
                    setError(error.response.data.errorMessage);
                } else {
                    setError("An unexpected error occurred.");
                }
            });
    }, []);

    return (
        <div>
            <h1>OAuth Login</h1>
            {error && <div style={{ color: 'red' }}>{error}</div>}
            {user ? (
                <div>
                    <h2>Welcome, {user.name}</h2>
                    <img src={user.picture} alt={user.name} />
                </div>
            ) : (
                <a href="/oauth2/authorization/google">Login with Google</a>
            )}
        </div>
    );
};

export default App;
```

---

## **Testing the Application**

### **Validation Scenarios**

1. **Invalid Email Format**:
   - Returns `INVALID_EMAIL_FORMAT`.
   - JSON response:
     ```json
     {
         "errorCode": "INVALID_EMAIL_FORMAT",
         "errorMessage": "Invalid email format: test@"
     }
     ```

2. **Duplicate Email**:
   - Returns `DUPLICATE_EMAIL`.
   - JSON response:
     ```json
     {
         "errorCode": "DUPLICATE_EMAIL",
         "errorMessage": "The email is already in use: test@example.com"
     }
     ```

3. **Other Invalid Data**:
   - Handles invalid names and image URLs similarly.

---

## **Conclusion**

With the added validations for email format, name length, and image URL, as well as duplicate checks, your application is now more robust. Proper error handling ensures that users receive clear feedback, improving the overall user experience.

Implementing these practices can help ensure your authentication system is secure, reliable, and user-friendly.

