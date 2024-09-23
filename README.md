# User Email Update Feature

This project implements a feature that allows users to update their email addresses securely through an API.

## Features

1. **Route for Updating Email**
   - A new route has been added to the `user.js` file:
     ```javascript
     router.put('/me/email/:userId/:newEmail', AuthMiddleware([ROLE.CUSTOMER, ROLE.SPONSOR, ROLE.OWNER]), UserController.updateEmail);
     ```
   - This route allows authorized users (roles: CUSTOMER, SPONSOR, OWNER) to update their email addresses.

2. **Controller for Email Update**
   - A controller function `updateEmail` has been created:
     ```javascript
     exports.updateEmail = async (req, res) => { 
         const user = await UserServices.updateEmail(req.email, req.params);
         res.status(200).json(user);
     }
     ```
   - This function handles the request and response for updating the email.

3. **Service Logic for Email Update**
   - The `updateEmail` service function performs the following actions:
     ```javascript
     exports.updateEmail = async (email, params) => {
         const { error } = emailSchema.validate({ userId, newEmail });
         if (error) throw new ValidationError(error.details[0].message);

         const user = await User.findOne({ email: email });
         const newEmailExist = await User.findOne({ email: params.newEmail });

         if (!user) throw new NotFoundError('Account not found');
         if (!user.verified) throw new ValidationError('User not verified');
         if (newEmailExist) throw new ValidationError('New email already exists!');
         if (user.email === params.newEmail) throw new ValidationError('New email is the same as the current email!');

         user.email = params.newEmail;
         await user.save();
         return user;
     }
     ```
   - **Validation:**
     - The function uses Joi for input validation, ensuring that:
       - The new email format is valid.
       - The user exists and is verified.
       - The new email does not already exist in the system.
       - The new email is not the same as the current email.
   - If all checks pass, the new email is saved to the user's record.

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/yourproject.git
