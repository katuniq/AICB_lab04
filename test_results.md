# Test Results — TravelBuddy Agent (Lab 4)

---

## Test 1 — Direct Answer (Không cần tool)

**Input:** `Xin chào! Tôi đang muốn đi du lịch nhưng chưa biết đi đâu.`

**Kỳ vọng:** Agent chào hỏi, hỏi thêm về sở thích/ngân sách/thời gian. Không gọi tool.

**Kết quả thực tế:**
```
============================================================
TravelBuddy - Trợ lý Du lịch Thông minh
Gõ 'quit' để thoát
============================================================

Bạn: Xin chào! Tôi đang muốn đi du lịch nhưng chưa biết đi đâu.

TravelBuddy đang suy nghĩ...
Trả lời trực tiếp

TravelBuddy: Chào bạn! Thật tuyệt khi bạn muốn đi du lịch! Có rất nhiều địa điểm thú vị ở Việt Nam để bạn khám phá. Bạn có thể cho mình biết một chút về sở thích của bạn không? Bạn thích biển, núi, thành phố lớn hay những trải nghiệm văn 
hóa?

Ngoài ra, bạn có ngân sách dự kiến cho chuyến đi này không? Thông tin này sẽ giúp mình tư vấn tốt hơn cho bạn!

```

---

## Test 2 — Single Tool Call

**Input:** `Tìm giúp tôi chuyến bay từ Hà Nội đi Đà Nẵng`

**Kỳ vọng:** Gọi `search_flights("Hà Nội", "Đà Nẵng")`, liệt kê 4 chuyến bay.

**Kết quả thực tế:**
```
Bạn: Tìm giúp tôi chuyến bay từ Hà Nội đi Đà Nẵng

TravelBuddy đang suy nghĩ...
Gọi tool: search_flights({'origin': 'Hà Nội', 'destination': 'Đà Nẵng'})
Trả lời trực tiếp

TravelBuddy: Đây là danh sách chuyến bay từ Hà Nội đi Đà Nẵng mà bạn có thể tham khảo:

1. **Vietnam Airlines (economy)**:
   - Giờ bay: 06:00 -> 07:20
   - Giá: 1.450.000 ₫

2. **Vietnam Airlines (business)**:
   - Giờ bay: 14:00 -> 15:20
   - Giá: 2.800.000 ₫

3. **VietJet Air (economy)**:
   - Giờ bay: 08:30 -> 09:50
   - Giá: 890.000 ₫

4. **Bamboo Airways (economy)**:
   - Giờ bay: 11:00 -> 12:20
   - Giá: 1.200.000 ₫

Nếu bạn cần tìm khách sạn hoặc có ngân sách cụ thể cho chuyến đi, hãy cho mình biết nhé!
```

---

## Test 3 — Multi-Step Tool Chaining

**Input:** `Tôi ở Hà Nội, muốn đi Phú Quốc 2 đêm, budget 5 triệu. Tư vấn giúp!`

**Kỳ vọng:** Agent tự chuỗi nhiều bước:
1. `search_flights("Hà Nội", "Phú Quốc")` → tìm vé rẻ nhất (1.100.000đ)
2. `search_hotels("Phú Quốc", max_price_phù_hợp)` → gợi ý trong tầm giá
3. `calculate_budget(5000000, "ve_bay:1100000,khach_san:...")` → tính còn lại
4. Tổng hợp thành gợi ý hoàn chỉnh với bảng chi phí

**Kết quả thực tế:**
```
Bạn: Tôi ở Hà Nội, muốn đi Phú Quốc 2 đêm, budget 5 triệu. Tư vấn giúp!

TravelBuddy đang suy nghĩ...
Gọi tool: search_flights({'origin': 'Hà Nội', 'destination': 'Phú Quốc'})
Gọi tool: search_hotels({'city': 'Phú Quốc', 'max_price_per_night': 2500000})
Gọi tool: calculate_budget({'total_budget': 5000000, 'expenses': 'vé máy bay: 1350000, khách sạn: 1600000'})
Trả lời trực tiếp

TravelBuddy: Dưới đây là gợi ý cho chuyến đi của bạn từ Hà Nội đến Phú Quốc trong vòng 2 đêm:

### Chuyến bay:
- **VietJet Air (economy)**:
  - Giờ bay: 10:00 -> 12:15
  - Giá: 1.350.000 ₫

### Khách sạn:
1. **9Station Hostel** (2⭐):
   - Khu vực: Dương Đông
   - Giá: 200.000 ₫/đêm
   - Rating: 4.5

2. **Sol by Meliá** (4⭐):
   - Khu vực: Bãi Trường
   - Giá: 1.500.000 ₫/đêm
   - Rating: 4.2

3. **Lahana Resort** (3⭐):
   - Khu vực: Dương Đông
   - Giá: 800.000 ₫/đêm
   - Rating: 4.0

### Tổng chi phí ước tính:
- **Vé máy bay**: 1.350.000 ₫
- **Khách sạn (2 đêm)**: 1.600.000 ₫ (Giá Sol by Meliá)
- **Tổng chi**: 2.950.000 ₫

**Ngân sách ban đầu**: 5.000.000 ₫
**Còn lại**: 2.050.000 ₫

### Gợi ý thêm:
Nếu bạn muốn thưởng thức hải sản tươi ngon, hãy thử ghé các quán ở chợ đêm Phú Quốc nhé! Và đừng quên tham quan Vinpearl Land hoặc đi snorkeling ở đảo An Thới để trải nghiệm những điều thú vị!

Nếu bạn cần thêm thông tin nào hoặc đặt dịch vụ, cứ cho mình biết nhé!
```


---

## Test 4 — Missing Info / Clarification

**Input:** `Tôi muốn đặt khách sạn`

**Kỳ vọng:** Agent hỏi lại: thành phố nào? bao nhiêu đêm? ngân sách bao nhiêu? Không gọi tool vội.

**Kết quả thực tế:**
```
============================================================
TravelBuddy - Trợ lý Du lịch Thông minh
Gõ 'quit' để thoát
============================================================

Bạn: Tôi muốn đặt khách sạn

TravelBuddy đang suy nghĩ...
Trả lời trực tiếp

TravelBuddy: Chắc chắn rồi! Tuy nhiên, để mình có thể giúp bạn tốt hơn, bạn cho mình biết bạn muốn đặt khách sạn ở đâu 
và ngân sách bạn dự định chi cho mỗi đêm là bao nhiêu nhé?
```

---

## Test 5 — Guardrail / Refusal

**Input:** `Giải giúp tôi bài tập lập trình Python về linked list`

**Kỳ vọng:** Agent từ chối lịch sự, nói rằng chỉ hỗ trợ về du lịch.

**Kết quả thực tế:**
```
Bạn: Giải giúp tôi bài tập lập trình Python về linked list

TravelBuddy đang suy nghĩ...
Trả lời trực tiếp

TravelBuddy: Xin lỗi, nhưng mình chỉ là trợ lý du lịch của TravelBuddy, nên không thể giúp bạn với các bài tập lập trình. Tuy nhiên, nếu bạn cần thông tin về du lịch, đặt vé máy bay hay khách sạn, mình rất hạnh phúc để hỗ trợ bạn!  
```
