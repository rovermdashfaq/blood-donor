<!DOCTYPE html><html lang="en"><head><meta charset="UTF-8"><meta name="viewport" content="width=1024,initial-scale=1,maximum-scale=1,user-scalable=no"><title>Blood Donor Database</title><style>body{font-family:Arial,sans-serif;text-align:center;background-color:#f8f9fa;margin:0;padding:20px;min-width:1024px;overflow-x:auto}h2{color:#d9534f}button,input,select{padding:10px;margin:5px;border:1px solid #ddd;border-radius:5px;font-size:16px}input,select{width:90%;max-width:300px}button{background-color:#d9534f;color:#fff;cursor:pointer}button:hover{background-color:#c9302c}table{width:100%;margin-top:20px;border-collapse:collapse;background:#fff;box-shadow:0 4px 8px rgba(0,0,0,.1)}td,th{padding:7px;border:1px solid #ddd;text-align:center;font-size:60%}th{background-color:#d9534f;color:#fff}tr:nth-child(even){background-color:#f2f2f2}.autocomplete-items{position:absolute;background:#fff;border:1px solid #ddd;max-height:150px;overflow-y:auto;z-index:1000;width:90%;max-width:300px}.autocomplete-items div{padding:10px;cursor:pointer}.autocomplete-items div:hover{background-color:#f2f2f2}#addInfoContainer{margin-bottom:15px}#addInfoButton{background-color:#5cb85c;font-size:16px}#addInfoButton:hover{background-color:#4cae4c}#webDeveloper{position:absolute;top:10px;right:10px;font-size:12px;color:#d9534f;cursor:pointer}</style></head><body><div id="webDeveloper" onclick='window.open("https://mdashfaqurrahmantanim.blogspot.com/2024/11/mdashfaq.com.html?m=1","_blank")'>Web Developer: Md. Ashfaqur Rahman Tanim</div><h2>Blood Donor Database</h2><div id="addInfoContainer"><a href="https://forms.gle/NiMb2ajVZVR8aJWB8" target="_blank"><button id="addInfoButton">➕ Add Your Information</button></a></div><select id="searchBlood"><option value="">Select Blood Group</option><option value="A+">A+</option><option value="A-">A-</option><option value="B+">B+</option><option value="B-">B-</option><option value="AB+">AB+</option><option value="AB-">AB-</option><option value="O+">O+</option><option value="O-">O-</option></select><div style="position:relative;display:inline-block"><input type="text" id="searchDistrict" placeholder="Enter District" oninput='showSuggestions("district")'><div id="autocomplete-list" class="autocomplete-items"></div></div><div style="position:relative;display:inline-block"><input type="text" id="searchUpazila" placeholder="Enter Upazila" oninput='showSuggestions("upazila")'><div id="autocomplete-upazila" class="autocomplete-items"></div></div><button onclick="searchDonors()">Search</button><table id="donorTable"><tr><th>Added Time</th><th>Email</th><th>Blood Group</th><th>Phone</th><th>District</th><th>Upazila</th></tr></table><script>let donorData = [];
    let uniqueDistricts = new Set();
    let uniqueUpazilas = new Set();

    async function fetchDonors() {
        const sheetURL = 'https://docs.google.com/spreadsheets/d/1j5TYFrP2zDE6tPQaT2mzXR971x-jvZ9EtTYNwR4Kr2c/export?format=tsv';
        try {
            const response = await fetch(sheetURL);
            const data = await response.text();
            const rows = data.split("\n").map(row => row.split("\t"));
            donorData = rows.slice(1).map(row => ({
                addedTime: row[0],
                email: row[1],
                bloodGroup: row[2],
                phone: row[3],
                district: row[4].toUpperCase().trim(),
                upazila: row[5].toUpperCase().trim()
            }));

            donorData.forEach(donor => {
                uniqueDistricts.add(donor.district);
                uniqueUpazilas.add(donor.upazila);
            });

            displayDonors(donorData);
        } catch (error) {
            console.error("Error fetching data:", error);
            document.getElementById("donorTable").innerHTML = "<tr><td colspan='6'>Error loading data. Please try again.</td></tr>";
        }
    }

    function searchDonors() {
        let blood = document.getElementById("searchBlood").value.toUpperCase();
        let district = document.getElementById("searchDistrict").value.toUpperCase().trim();
        let upazila = document.getElementById("searchUpazila").value.toUpperCase().trim();

        let filtered = donorData.filter(donor =>
            (!blood || donor.bloodGroup === blood) &&
            (!district || donor.district === district) &&
            (!upazila || donor.upazila === upazila)
        );

        displayDonors(filtered);
    }

    function displayDonors(filteredData) {
        let tableHTML = "<tr><th>Added Time</th><th>Email</th><th>Blood Group</th><th>Phone</th><th>District</th><th>Upazila</th></tr>";
        if (filteredData.length === 0) {
            tableHTML += "<tr><td colspan='6' style='color:red; font-size:18px;'>No Donor Found</td></tr>";
        } else {
            filteredData.forEach(donor => {
                tableHTML += `<tr>
                    <td>${donor.addedTime}</td>
                    <td>${donor.email}</td>
                    <td>${donor.bloodGroup}</td>
                    <td><a href="tel:${donor.phone}" style="color:blue;">${donor.phone}</a></td>
                    <td>${donor.district}</td>
                    <td>${donor.upazila}</td>
                </tr>`;
            });
        }
        document.getElementById("donorTable").innerHTML = tableHTML;
    }

    function showSuggestions(type) {
        let input = document.getElementById(type === 'district' ? "searchDistrict" : "searchUpazila");
        let list = document.getElementById(type === 'district' ? "autocomplete-list" : "autocomplete-upazila");
        list.innerHTML = "";

        let suggestions = Array.from(type === 'district' ? uniqueDistricts : uniqueUpazilas)
            .filter(item => item.startsWith(input.value.toUpperCase()))
            .slice(0, 5);

        suggestions.forEach(item => {
            let div = document.createElement("div");
            div.textContent = item;
            div.onclick = () => { input.value = item; list.innerHTML = ""; };
            list.appendChild(div);
        });

        document.addEventListener("click", function() {
            list.innerHTML = "";
        }, { once: true });
    }

    fetchDonors();</script></body></html>
