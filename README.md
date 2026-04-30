# 🧠 FULL FLOW: SPRING BOOT POST USER → DB ([Project link](<https://github.com/nitin-is-me/spring-signup-api> "Project link"))

---

# 🔥 1. Big Picture (Understand THIS first)

Your app flow is:

```
Client (Postman / frontend)
        ↓
Controller (handles HTTP)
        ↓
Service (business logic)
        ↓
Repository (DB interaction)
        ↓
Database (PostgreSQL)
```

👉 Each layer has ONE job. Don’t mix them.

---

# 🧩 2. Your User Entity ([User.java](<http://User.java>))

```
@Entity
@Table(name = "users")
public class User {
```

👉 This tells Spring:

> “This class = table in database”

---

## Fields:

```
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

👉 Primary key (auto increment)

---

```
@Column(unique = true, nullable = false)
private String username;
```

👉 Must be unique + cannot be null

---

```
@Column(nullable = false)
private String password;
```

👉 Required field

---

## 🧠 What happens because of this?

Spring + Hibernate:  
👉 creates a table `users` automatically  
👉 maps this class ↔ table

---

# 🗄️ 3. Repository Layer

```
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
}
```

👉 This means:

> “Spring, create a DB handler for User table”

---

## What you get automatically:

```
save(user)
findById(id)
findAll()
deleteById(id)
```

👉 You didn’t write them. Spring did.

---

## 🧠 Important

- This is an **interface**

- Spring creates implementation behind the scenes

- That object = **bean**

---

# 🧠 4. Service Layer (UserService)

```
@Service
public class UserService {
```

👉 Means:

> “This class contains business logic”

Spring:  
👉 creates object (bean)

---

## Dependencies

```
@Autowired
private BCryptPasswordEncoder passwordEncoder;

@Autowired
private UserRepository userRepository;
```

👉 Means:

> “Spring, give me these objects”

Spring:

- already created them

- injects them here

---

## Main Logic

```
public User registerUser(User user){
    String encodedPassword = passwordEncoder.encode(user.getPassword());

    user.setPassword(encodedPassword);
    return userRepository.save(user);
}
```

---

### Step-by-step:

1. Take raw password

2. Hash it (security)

3. Replace password

4. Save user in DB

---

# 🌐 5. Controller Layer (AuthController)

```
@RestController
@RequestMapping("/api/auth")
public class AuthController {
```

👉 Handles HTTP requests

---

## Dependency

```
@Autowired
private UserService userService;
```

👉 “Spring, give me UserService”

---

## API Endpoint

```
@PostMapping("/signup")
public User signup(@RequestBody User user){
    return userService.registerUser(user);
}
```

---

## 🧠 What `@RequestBody` does

Incoming JSON:

```
{
  "username": "nitin",
  "password": "123"
}
```

👉 Converted to:

```
User user
```

---

# ⚙️ 6. Security Config (ProjectConfig)

```
@Bean
public BCryptPasswordEncoder passwordEncoder(){
    return new BCryptPasswordEncoder();
}
```

👉 Creates encoder bean

---

```
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http)
```

👉 Defines rules:

- `/api/auth/**` → open

- everything else → protected

---

# 🗄️ 7. [application.properties](<http://application.properties>)

```
spring.datasource.url=...
spring.datasource.username=...
spring.datasource.password=...
```

👉 DB connection

---

```
spring.jpa.hibernate.ddl-auto=update
```

👉 Automatically creates/updates tables

---

```
spring.jpa.show-sql=true
```

👉 Shows SQL in console

---

# 🔥 8. FULL RUNTIME FLOW (IMPORTANT)

### Step 1: Request comes

```
POST /api/auth/signup
```

---

### Step 2: Controller

```
signup(@RequestBody User user)
```

👉 JSON → User object

---

### Step 3: Service

```
registerUser(user)
```

👉 password encoded  
👉 user prepared

---

### Step 4: Repository

```
save(user)
```

👉 SQL generated  
👉 INSERT into DB

---

### Step 5: DB

User stored in `users` table

---

# 🧠 9. What is a BEAN (final clarity)

👉 Bean = object managed by Spring

Examples in your app:

- UserService

- UserRepository

- BCryptPasswordEncoder

---

# 🔥 10. What does `@Autowired` REALLY mean

👉 “Spring, give me that bean”

Without it:

```
userService = null ❌
```

---

# 🧠 11. Why not create objects manually?

Because:

❌ You can’t create `UserRepository`  
❌ Too many dependencies  
❌ messy code

Spring solves all that.

---

# 💥 12. Final Mental Model (THIS IS GOLD)

- `@Entity` → table

- `@Repository` → DB access

- `@Service` → logic

- `@RestController` → API

- `@Bean` → manually create object

- `@Autowired` → ask Spring for object

---

# ⚡ ONE LINE SUMMARY

👉 Controller takes input → Service processes → Repository saves → DB stores  
👉 Spring creates and connects everything automatically using beans

