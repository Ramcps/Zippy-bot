<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Zippy Hotel Booking</title>
    <style>
        body {
            font-family: 'Segoe UI', Arial, sans-serif;
            max-width: 700px;
            margin: 20px auto;
            padding: 10px;
            background: linear-gradient(to bottom, #e0f7fa, #f4f4f4);
        }
        .form-container {
            padding: 25px;
            border-radius: 15px;
            background: white;
            box-shadow: 0 4px 10px rgba(0,0,0,0.2);
        }
        h2 {
            text-align: center;
            color: #1a73e8;
            margin-bottom: 20px;
        }
        input, button {
            width: 100%;
            padding: 12px;
            margin: 10px 0;
            font-size: 16px;
            border-radius: 8px;
            border: 1px solid #ccc;
            box-sizing: border-box;
        }
        input:focus {
            outline: none;
            border-color: #1a73e8;
            box-shadow: 0 0 5px rgba(26, 115, 232, 0.3);
        }
        button {
            background: #25D366;
            color: white;
            border: none;
            cursor: pointer;
            font-weight: bold;
            transition: background 0.3s;
        }
        button:hover {
            background: #20b958;
        }
        #viewBookings {
            background: #1a73e8;
        }
        #viewBookings:hover {
            background: #1557b0;
        }
        #confirmation {
            display: none;
            color: #25D366;
            font-size: 18px;
            text-align: center;
            margin-top: 20px;
            padding: 15px;
            border: 2px solid #25D366;
            border-radius: 8px;
            background: #e8f5e9;
        }
        #bookingsList {
            margin-top: 20px;
            display: none;
        }
        #bookingsList p {
            padding: 12px;
            border-bottom: 1px solid #ddd;
            background: #f9f9f9;
            border-radius: 5px;
            margin: 5px 0;
        }
        @media (max-width: 600px) {
            input, button {
                font-size: 14px;
            }
            .form-container {
                padding: 15px;
            }
            h2 {
                font-size: 24px;
            }
        }
    </style>
</head>
<body>
    <h2>Zippy Hotel Booking</h2>
    <div class="form-container">
        <form id="bookingForm">
            <input type="text" id="city" placeholder="Enter city (e.g., Hyderabad)" required>
            <input type="text" id="hotel" placeholder="Hotel name (e.g., Taj Krishna)" required>
            <input type="text" id="roomType" placeholder="Room type (e.g., Deluxe, Suite)" required>
            <input type="date" id="checkIn" placeholder="Check-in date" required>
            <input type="date" id="checkOut" placeholder="Check-out date" required>
            <input type="number" id="guests" placeholder="Number of guests (min 1)" min="1" required>
            <button type="submit">Book via WhatsApp</button>
        </form>
        <p id="confirmation"></p>
        <button id="viewBookings">View Past Bookings</button>
        <div id="bookingsList"></div>
    </div>

    <script>
        document.getElementById("bookingForm").addEventListener("submit", function(e) {
            e.preventDefault();
            let city = document.getElementById("city").value.trim();
            let hotel = document.getElementById("hotel").value.trim();
            let roomType = document.getElementById("roomType").value.trim();
            let checkIn = new Date(document.getElementById("checkIn").value);
            let checkOut = new Date(document.getElementById("checkOut").value);
            let guests = parseInt(document.getElementById("guests").value);
            let today = new Date();
            today.setHours(0, 0, 0, 0);

            // Validation
            if (!city || city.length < 2) {
                alert("Please enter a valid city name (min 2 characters).");
                return;
            }
            if (!hotel || hotel.length < 2) {
                alert("Please enter a valid hotel name (min 2 characters).");
                return;
            }
            if (!roomType || roomType.length < 2) {
                alert("Please enter a valid room type (e.g., Deluxe, Suite).");
                return;
            }
            if (!checkIn || checkIn < today) {
                alert("Check-in date must be today or later.");
                return;
            }
            if (!checkOut || checkOut <= checkIn) {
                alert("Check-out date must be after check-in date.");
                return;
            }
            if (!guests || guests < 1) {
                alert("Number of guests must be at least 1.");
                return;
            }

            // Simulate booking
            let booking = {
                city: city,
                hotel: hotel,
                roomType: roomType,
                checkIn: checkIn.toISOString().split('T')[0],
                checkOut: checkOut.toISOString().split('T')[0],
                guests: guests,
                bookingId: `ZIPPY${Math.floor(Math.random() * 10000).toString().padStart(4, '0')}`
            };

            // Save to localStorage
            let bookings = JSON.parse(localStorage.getItem("bookings") || "[]");
            bookings.push(booking);
            localStorage.setItem("bookings", JSON.stringify(bookings));

            // WhatsApp message
            let message = `Booking Request:\nHotel: ${hotel}, ${city}\nRoom Type: ${roomType}\nGuests: ${guests}\nCheck-in: ${booking.checkIn}\nCheck-out: ${booking.checkOut}\nBooking ID: ${booking.bookingId}\nPlease confirm or suggest other options.`;
            let waNumber = "YOUR_PHONE_NUMBER"; // Replace with your WhatsApp number (e.g., +919876543210)
            let waLink = `https://wa.me/${waNumber}?text=${encodeURIComponent(message)}`;
            try {
                window.open(waLink, '_blank') || alert("Failed to open WhatsApp. Please check your connection.");
            } catch (error) {
                alert("Error sending booking request. Try again later.");
            }

            // Show confirmation
            document.getElementById("confirmation").style.display = "block";
            document.getElementById("confirmation").innerText = 
                `Booking Request Sent!\nBooking ID: ${booking.bookingId}\nHotel: ${booking.hotel}, ${booking.city}\nRoom: ${booking.roomType}\nGuests: ${booking.guests}\nDates: ${booking.checkIn} to ${booking.checkOut}\nCheck WhatsApp for confirmation!`;
            document.getElementById("bookingForm").reset();
        });

        // View past bookings
        document.getElementById("viewBookings").addEventListener("click", function() {
            let bookings = JSON.parse(localStorage.getItem("bookings") || "[]");
            let bookingsList = document.getElementById("bookingsList");
            bookingsList.style.display = "block";
            if (bookings.length === 0) {
                bookingsList.innerText = "No bookings found.";
                return;
            }
            bookingsList.innerHTML = "<h3>Past Bookings</h3>";
            bookings.forEach(booking => {
                bookingsList.innerHTML += `<p>Booking ID: ${booking.bookingId} | Hotel: ${booking.hotel}, ${booking.city} | Room: ${booking.roomType} | Guests: ${booking.guests} | Dates: ${booking.checkIn} to ${booking.checkOut}</p>`;
            });
        });
    </script>
</body>
</html>
