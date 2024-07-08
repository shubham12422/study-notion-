# study-notion-

const User = require("../models/User");
const mailsender = require("../utils/mailSender");
const bcrypt= require("bcrypt");



// reset password token : 

exports. resetPasswordToken = async (req,res) => {


    // get email from the body 
    // cheak user for this email , email validation :
    // generate token
    // user update and expiration time
    // create url
    // return responce

    const email = req.body.email;
    

    const user = await User.findOne({email:email});
    if (!user){
        return res.json({
            success:false,
            message:"user not  registered found",
        })
    }


    const token = crypto.randomUUID();

    const updateDetails = await User.findByIdAndUpdate(
        {email:email},
        {
            token:token,
            resetPasswordExpires: Date.now() + 5*60*1000,
        },
        {new:true}
    );





    const url = `http://localhost:3000/update-passsword/${token}`



    await mailSender(email,"password reset link", `password reset link ${url}`)




    return res.json({
        success:true,
        message:"email send successfully"
    })

}



exports.resetPassword = async(req,res) => {

    const { password, confirmPassword,token} = req.body;

    if (password !== confirmPassword){

        return json({
            success:false,
            message:"password doest match with confirm password",
        })
    }



    const user =  await user.findone({token : token});



    if (!userDetails) {
        return res.json({
            success:false,
            message:`token is invalid`,
        });
    }


    if ( userDetails.resetPasswordExpires < Date.now() ){


        return res.json({
            sucess:false,
            message:"time expired"
        })


    }



    const hashedPassword = await bcrypt.hash(password,10);


    await User.findOneAndUpdate(
        {
            token:token
        },
        {
            password:hashedPassword,
        },
        {new:true},
    )




    return res.status(200).json({
        sucess:true,
        message : `password successfully set`
    })
}
