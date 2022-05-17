# Code Sample For MLH Program

```
const nodemailer = require("nodemailer");
const { oAuthClient } = require("../config/oAuth");
require("dotenv").config();

class MailSender {
    async sendMail(to = "", subject = "", message = "", back = (a, b) => {}) {
        try {
            const accessToken = await oAuthClient.getAccessToken();

            const transporter = nodemailer.createTransport({
                service: "gmail",
                auth: {
                    type: "oauth2",
                    user: process.env.GMAIL_USER,
                    clientId: process.env.CLIENT_ID,
                    clientSecret: process.env.CLIENT_SECRET,
                    refreshToken: process.env.REFRESH_TOKEN,
                    accessToken: accessToken,
                },
            });

            let info = await transporter.sendMail({
                from: process.env.MAILING_DOMAIN,
                to: to,
                subject: subject,
                html: message,
            });
            console.log("Message sent: %s", info.messageId);
            console.log("Preview URL: %s", nodemailer.getTestMessageUrl(info));
            back(null, true);
            return {
                success: true,
                error: null,
            };
        } catch (e) {
            console.error("Error Occured while trying to send email to %s", to);
            console.error(e);
            back(e, false);
            return {
                success: false,
                error: e,
            };
        }
    }
}

module.exports = MailSender;
```
