# ConnectX सर्वर — पूरी जानकारी

## यह सर्वर क्या करता है?
- फ़ोटो और वीडियो Cloudinary पर जमा करता है
- सन्देश सभी उपयोगकर्ताओं को असली समय में पहुँचाता है
- कौन ऑनलाइन है यह बताता है

---

## गिटहब पर कैसे डालें?

### पहला कदम — गिटहब पर नया भंडार बनाओ
1. github.com पर जाओ
2. ऊपर दाईं तरफ़ "+" पर क्लिक करो
3. "New repository" चुनो
4. नाम दो: **connectx-server**
5. "Create repository" पर क्लिक करो

### दूसरा कदम — फ़ाइलें अपलोड करो
1. "uploading an existing file" पर क्लिक करो
2. **server.js** और **package.json** दोनों फ़ाइलें खींचकर डालो
3. "Commit changes" पर क्लिक करो

---

## Cloudinary पर मुफ़्त खाता बनाओ (फ़ोटो जमा करने के लिए)

1. cloudinary.com पर जाओ
2. "Sign Up Free" पर क्लिक करो
3. खाता बनाओ — बिल्कुल मुफ़्त है
4. खाता बनने के बाद Dashboard पर जाओ
5. वहाँ तुम्हें तीन चीज़ें मिलेंगी:
   - **Cloud Name**
   - **API Key**
   - **API Secret**
6. इन तीनों को कहीं लिख लो

---

## Render पर मुफ़्त सर्वर चलाओ

1. render.com पर जाओ
2. गिटहब से लॉगिन करो
3. "New +" → "Web Service" पर क्लिक करो
4. अपना **connectx-server** भंडार चुनो
5. यह भरो:
   - **Build Command:** `npm install`
   - **Start Command:** `npm start`
6. नीचे "Environment Variables" में यह तीन चीज़ें डालो:
   - `CLOUDINARY_CLOUD_NAME` = तुम्हारा Cloud Name
   - `CLOUDINARY_API_KEY` = तुम्हारी API Key
   - `CLOUDINARY_API_SECRET` = तुम्हारा API Secret
7. "Create Web Service" पर क्लिक करो
8. कुछ मिनट में सर्वर चालू हो जाएगा
9. Render तुम्हें एक लिंक देगा जैसे: **https://connectx-server.onrender.com**

---

## यह लिंक मिलने के बाद क्या करें?

यह लिंक मुझे बताओ, मैं तुम्हारी **kachifile.html** को इस सर्वर से जोड़ दूंगा।
उसके बाद फ़ोटो, वीडियो सब कुछ काम करेगा।
