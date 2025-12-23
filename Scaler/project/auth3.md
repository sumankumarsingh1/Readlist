# Authorization 3
Agenda:
* Cookies
* CORS
* Complete the login API

## Cookies
* Cookies are the temporary storage at client's end and stored in browser
* Cookies are having some data structure to store the information related to user session
## CORS (Cross Origin Resource Sharing)
* Accessing resources from other Origin
* scaler.com → aws.com (s3, buckets)
* scaler.com → gfg.com (image, tree.jpeg)
* If CORS is disabled in browser we will not be able to access the resources from other origins
* Origin = Protocol + Domain + Port
* CORS is a security feature in browser that controls how a web page can request resources like (scripts, fonts, images) from another origin. It is designed to prevent malicious scripts to interact with our pages unless explicitly allowed

## Token Generation

In the previous class we had completed the authentication APIs 
* signup
* login

Now we will work on generating the token.

AuthService.java
```
...
    public User login(String email, String password){
        Optional<User> optionalUser = userRepo.findByEmail(email);
        if(optionalUser.isEmpty()){
            return null;
        }
        User user = optionalUser.get();
        if (!bCryptPasswordEncoder.matches(password,user.getPassword())){
            return null;
        }
        // Token Generation

        
        return user;
    }
...
```
Now we will resume working on Token Generation. For implementing the token generation we will add some dependencies in the pom.xml
```
<dependencies>
    <!-- JJWT API (required) -->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-api</artifactId>
        <version>0.13.0</version>
    </dependency>

    <!-- JJWT Implementation (required) -->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-impl</artifactId>
        <version>0.13.0</version>
        <scope>runtime</scope>
    </dependency>

    <!-- JJWT JSON Processing Library (choose one: Jackson or Gson) -->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-jackson</artifactId>
        <version>0.13.0</version>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

After adding the dependencies in the pom.xml file we will do "Right click on pom.xml > Maven > Reload project" so that the dependencies are downloaded.

For generating the token we will modify the code for login in AuthService.java as below:

```
    public Pair<User, MultiValueMap<String, String>> login(String email, String password){
        Optional<User> optionalUser = userRepo.findByEmail(email);
        if(optionalUser.isEmpty()){
            return null;
        }
        User user = optionalUser.get();
        if (!bCryptPasswordEncoder.matches(password,user.getPassword())){
            return null;
        }
        // Token Generation
        String message = "{\n" +
            "  \"email\" : \"someemail1@somedomain.com\",\n" +
            "  \"roles\" : [\n" +
            "    \"instructor\",\n" +
            "    \"buddy\" \n" +
            "  ]\n" +
            "  \"expirationDate\" : \"25thJuly2025\",\n" +
        "}";
        byte[] content = message.getBytes(StandardCharsets.UTF_8);
        String token = Jwts.builder().content(content).compact();
        MultiValueMap<String,String> headers = new LinkedMultiValueMap<>();
        headers.add(HttpHeaders.SET_COOKIE,token);

        return new Pair<User,MultiValueMap<String,String>>(user,headers);
    }
```
* We wrote the code to generate the token
* We modified the return type
* We used Pair class to return user as well as token

Now we also need to update the Interface to match the return type in IAuthService.java

```
    public Pair<User, MultiValueMap<String, String>> login(String email, String password);
```

And then update the AuthController.java as below:
```
    @PostMapping("/login")
    public ResponseEntity<UserDto> login(@RequestBody LoginRequestDto loginRequestDto){
        Pair<User, MultiValueMap<String,String>> userWithHeaders = authService.login(loginRequestDto.getEmail(),loginRequestDto.getPassword());
        User user = userWithHeaders.a;
        if(user == null){
            throw new RuntimeException("Bad Credentials");
        }
        return new ResponseEntity<>(from(user),userWithHeaders.b,HttpStatus.OK);
    }
```

We further modify the code login code in the AuthService.java to handle the claims instead of content from the user object.

```

    public Pair<User, MultiValueMap<String, String>> login(String email, String password){
        Optional<User> optionalUser = userRepo.findByEmail(email);
        if(optionalUser.isEmpty()){
            return null;
        }
        User user = optionalUser.get();
        if (!bCryptPasswordEncoder.matches(password,user.getPassword())){
            return null;
        }
        // Token Generation
        Map<String,Object>  claims = new HashMap<>();
        claims.put("user_id__",user.getId());
        claims.put("roles",user.getRoles());
        claims.put("email",user.getEmail());
        long nowInMillis = System.currentTimeMillis();
        claims.put("iat",nowInMillis);
        claims.put("exp",nowInMillis+6.048e+8);


        MacAlgorithm algorithm = Jwts.SIG.HS256;
        SecretKey secretKey = algorithm.key().build();
        String token = Jwts.builder().claims(claims).signWith(secretKey).compact();
        MultiValueMap<String,String> headers = new LinkedMultiValueMap<>();
        headers.add(HttpHeaders.SET_COOKIE,token);

        return new Pair<User,MultiValueMap<String,String>>(user,headers);
    }
```

Also check the token generated in https://www.jwt.io/

This token will be validated against some resources server. We will have to store the token. 
* Extract the token which we are getting in request
* match that with the token stored in our database
* decrypt token to get the expiry date
* if it is not expired then the token is valid

**Additional Reading**: How to store the secrets in the Key Vaults.

* Cookies are stored at browser level 
* Sessions are stored at server level

### Session Class
Let us create a new class Session

model/Session.java
```
package com.scaler.suman.UserAuthService1.models;

import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.ManyToOne;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Entity
public class Session extends BaseModel {
    @ManyToOne
    private User user;
    private  String token;
    @Enumerated(EnumType.ORDINAL)
    private SessionState sessionState;
}
```

models/SessionState.java
```
package com.scaler.suman.UserAuthService1.models;

public enum SessionState {
    ACTIVE, INACTIVE
}
```

repos/SessionRepo.java
```
package com.scaler.suman.UserAuthService1.repos;

import com.scaler.suman.UserAuthService1.models.Session;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface SessionRepo extends JpaRepository<Session, Long> {
}
```

Now we update the AuthService.java login function to save the session:

```
        // Save Session
        Session session = new Session();
        session.setSessionState(SessionState.ACTIVE);
        session.setUser(user);
        session.setToken(token);
        sessionRepo.save(session);
```

Now we validate the token:
To validate the token we are introducting a new function validateToken with mapping (/validate)


