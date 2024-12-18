# Electrical-Supplier-App-Android-Studios-

Creating an Electrical Supplier App in Android Studio to manage supply and demand between vendors, contractors, and manufacturers involves designing an application with various user interfaces (UIs) and back-end logic to handle orders, inventory, communication, and more.
We’ll outline the core features and provide a basic structure for the application, which includes:	
•	User management (vendors, contractors, and manufacturers)	•	Order management system for products	
•	Inventory management	
•	Communication between vendors, contractors, and manufacturers	
•	Backend database (using Firebase or a local database like SQLite)

Key Features for the App:	
1.	User Authentication:
•	Different roles (Vendors, Contractors, Manufacturers).
•	Login & Registration for each role.	
3.	Inventory Management:
•	Vendors can manage the inventory (products, prices, availability).
3.	Order Management:
•	Contractors can place orders from vendors.
•	Vendors can manage incoming orders.
•	Manufacturers can see the orders from vendors and update stock.
4.	Notifications:
•	Notifications for orders, product availability, etc.
5.	Chat System:
•	Communication between vendors, contractors, and manufacturers (optional).

Steps to Create the App:
1. Set up the Project:
•	Open Android Studio and create a new project with an Empty Activity.
•	Select Kotlin as the programming language.
•	Set up Firebase for user authentication and Firestore database, or SQLite for local storage.

2. Dependencies:
In your build.gradle (Module: app) file, add the dependencies for Firebase, authentication, and Firestore.
dependencies {
implementation "com.google.firebase:firebase-auth:21.0.3"
implementation "com.google.firebase:firebase-firestore:24.3.0"
implementation "com.google.firebase:firebase-messaging:23.1.0"
implementation "androidx.recyclerview:recyclerview:1.2.1"
implementation "androidx.lifecycle:lifecycle-extensions:2.2.0"
implementation 'com.google.android.material:material:1.6.1'}
}



Don’t forget to sync your project with Gradle after adding dependencies.



3. Firebase Authentication (Login/Sign-Up)
Set up Firebase Authentication to manage user roles (Vendors, Contractors, Manufacturers).
Firebase Setup:	•	Go to Firebase Console -> Add Firebase to your Android app.	•	Enable Email/Password authentication in Firebase Authentication.
Authentication Code:
class LoginActivity : AppCompatActivity() {
   private lateinit var auth: FirebaseAuth
   private lateinit var emailField: EditText
   private lateinit var passwordField: EditText
   private lateinit var loginButton: Button
   private lateinit var registerButton: Button
   
    override fun onCreate(savedInstanceState: Bundle?) {        
    super.onCreate(savedInstanceState)        
    setContentView(R.layout.activity_login)
     
        auth = FirebaseAuth.getInstance()
        
        
        emailField = findViewById(R.id.emailField)        
        passwordField = findViewById(R.id.passwordField)        
        loginButton = findViewById(R.id.loginButton)        
        registerButton = findViewById(R.id.registerButton)
        
        loginButton.setOnClickListener {            
        val email = emailField.text.toString()            
        val password = passwordField.text.toString()
            
            auth.signInWithEmailAndPassword(email, password)                
            .addOnCompleteListener(this) { task ->                    
            if (task.isSuccessful) {                        
            // Redirect based on user role                        
            val user = auth.currentUser                        
            if (user != null) {                            
            checkUserRole(user.uid)                        
            }                    
            }else {                        
            Toast.makeText(baseContext, "Authentication failed.", 
            Toast.LENGTH_SHORT).show()                    
            }                
          }        
        }
        registerButton.setOnClickListener {            
        // Navigate to RegisterActivity for new user registration        
        }    
      }
    
    private fun checkUserRole(userId: String) {        
    val db = FirebaseFirestore.getInstance()        
    val usersRef = db.collection("users")        
    usersRef.document(userId).get().addOnSuccessListener { document ->            
    if (document.exists()) {                
       val role = document.getString("role") // role can be "vendor", "contractor", or "manufacturer"                
       if (role == "vendor") {                    
          startActivity(Intent(this, VendorActivity::class.java))                
          } else if (role == "contractor") {                    
             startActivity(Intent(this, ContractorActivity::class.java))                
             } else {                    
             startActivity(Intent(this, ManufacturerActivity::class.java))                
             }            
          }        
          
        }    
      }
  }

4. Role-Based Navigation:
Depending on the user’s role, navigate them to different activities. For example:
•	Vendors can see inventory and manage stock.	•	Contractors can place orders.	•	Manufacturers can manage orders and update product availability.


5. Firestore Database for Inventory and Orders



Use Firebase Firestore to store and manage inventory, orders, and user data.
Firestore Structure:



users

  |-- userId

       |-- name: "John Doe"

       |-- email: "john@example.com"

       |-- role: "vendor"

       |-- products (for vendors): 

           |-- productId

               |-- name: "Wire"

               |-- price: "100"

               |-- quantity: "500"

orders

  |-- orderId

       |-- contractorId: "contractorId"

       |-- vendorId: "vendorId"

       |-- products: 

           |-- productId: "productId"

           |-- quantity: 5

           |-- price: 100



Adding Product to Inventory:



fun addProductToInventory(product: Product) {

    val db = FirebaseFirestore.getInstance()

    val productData = hashMapOf(

        "name" to product.name,

        "price" to product.price,

        "quantity" to product.quantity

    )

    db.collection("users").document(userId).collection("products")

        .add(productData)

        .addOnSuccessListener {

            Toast.makeText(this, "Product added successfully", Toast.LENGTH_SHORT).show()

        }

        .addOnFailureListener {

            Toast.makeText(this, "Error adding product", Toast.LENGTH_SHORT).show()

        }

}

6. Order Management System



Contractors place orders by selecting products from the vendor’s inventory. Vendors can view and manage these orders.



Placing an Order (Contractor Side):



fun placeOrder(order: Order) {

    val db = FirebaseFirestore.getInstance()

    val orderData = hashMapOf(

        "contractorId" to order.contractorId,

        "vendorId" to order.vendorId,

        "products" to order.products,

        "status" to "Pending"

    )

    db.collection("orders").add(orderData)

        .addOnSuccessListener {

            Toast.makeText(this, "Order placed successfully", Toast.LENGTH_SHORT).show()

        }

        .addOnFailureListener {

            Toast.makeText(this, "Error placing order", Toast.LENGTH_SHORT).show()

        }

}



Viewing and Managing Orders (Vendor Side):



fun getOrdersForVendor() {

    val db = FirebaseFirestore.getInstance()

    val ordersRef = db.collection("orders").whereEqualTo("vendorId", vendorId)



    ordersRef.get().addOnSuccessListener { result ->

        for (document in result) {

            val order = document.toObject(Order::class.java)

            // Display order details

        }

    }

}


7. UI/UX Design:
•	Use RecyclerView to display lists of products, orders, etc.
•	Create intuitive forms for order placing and inventory management.
•	Use CardView to display each product or order in a clean manner.
Example layout for displaying products in activity_vendor.xml:
<androidx.recyclerview.widget.RecyclerView
android:id="@+id/productRecyclerView"
android:layout_width="match_parent"
android:layout_height="match_parent"/>

9. Notifications (Optional):
Use Firebase Cloud Messaging (FCM) to send notifications to vendors, contractors, and manufacturers about order status, stock updates, or new product availability.

10. Test & Debug the App:

• Test the app thoroughly with different users (vendor, contractor, manufacturer).

• Debug any issues related to Firebase connectivity or data handling.



Conclusion:



This app structure provides the basic framework for a multi-role Electrical Supplier App that connects vendors, contractors, and manufacturers. The app allows vendors to manage their inventory, contractors to place orders, and manufacturers to handle production and order fulfillment. You can expand this basic structure with features like a chat system, order tracking, and real-time stock updates.
