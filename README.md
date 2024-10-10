<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تطبيق العميل - حجز سيارة</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            direction: rtl;
            padding: 20px;
        }

        button {
            padding: 10px;
            background-color: #007BFF;
            color: white;
            border: none;
            cursor: pointer;
        }

        button:hover {
            background-color: #0056b3;
        }

        #client-map {
            height: 400px;
        }
    </style>
</head>
<body>
    <!-- واجهة العميل -->
    <h2>مرحبا بك كعميل</h2>
    <button id="request-ride">طلب سيارة الآن</button>
    <div id="client-map"></div>

    <!-- Firebase SDKs -->
    <script src="https://www.gstatic.com/firebasejs/9.1.3/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.1.3/firebase-firestore.js"></script>

    <script>
        // تكوين Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyCow0kJbcJ7OUzaHOBqXp1hF1LPSxb6dBo",
            authDomain: "alnashme-tawsel.firebaseapp.com",
            projectId: "alnashme-tawsel",
            storageBucket: "gs://alnashme-tawsel.appspot.com",
            messagingSenderId: "76863416572",
            appId: "1:76863416572:android:e75569b3ef02142baf8f79",
            databaseURL: "https://alnashme-tawsel-default-rtdb.firebaseio.com/"
        };

        // تهيئة Firebase
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();

        // طلب إذن الوصول إلى الموقع وتحديد الموقع الحالي
        function requestLocationPermission() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(success, error, {
                    enableHighAccuracy: true
                });
            } else {
                alert("المتصفح لا يدعم خدمات تحديد الموقع.");
            }
        }

        function success(position) {
            const lat = position.coords.latitude;
            const lon = position.coords.longitude;
            document.getElementById('client-map').innerHTML = `موقعك الحالي: (${lat}, ${lon})`;
            sendLocationToFirebase(lat, lon);
        }

        function error() {
            alert("يجب تفعيل خدمة تحديد الموقع (GPS) لاستخدام التطبيق.");
        }

        // إرسال موقع العميل إلى Firebase
        function sendLocationToFirebase(lat, lon) {
            db.collection('rides').add({
                pickup: {
                    latitude: lat,
                    longitude: lon
                },
                status: 'pending',
                timestamp: firebase.firestore.FieldValue.serverTimestamp()
            }).then(() => {
                alert('تم حجز السيارة بنجاح!');
            });
        }

        // معالجة زر "طلب سيارة الآن"
        document.getElementById('request-ride').addEventListener('click', function() {
            requestLocationPermission(); // طلب إذن تحديد الموقع
        });

    </script>
</body>
</html>
