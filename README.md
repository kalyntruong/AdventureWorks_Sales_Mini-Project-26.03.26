# AdventureWorks_Sales_Mini-Project-26.03.26
Mini Project phân tích dữ liệu AdventureWorks bằng SQL 
---PHẦN I: TỔNG QUAN SỨC KHỎE DOANH NGHIỆP 
/* Task 1: Phân tích xu hướng Doanh thu thuần theo từng tháng/năm */

SELECT * FROM Sales.SalesOrderHeader;
SELECT 
    YEAR(OrderDate) AS 'SalesYear',
    MONTH(OrderDate) AS 'SalesMonth',
    FORMAT(SUM(SubTotal), 'N2') AS 'MonthlyNetRevenue', 
    COUNT(SalesOrderID) AS 'TotalOrders'
FROM Sales.SalesOrderHeader
WHERE Status = 5  
GROUP BY YEAR(OrderDate), MONTH(OrderDate)
ORDER BY SalesYear ASC, SalesMonth ASC;

---PHẦN II: PHÂN TÍCH VÀ PHÂN LOẠI CHI TIẾT 
/* Task 2: Phân tích % đóng góp doanh thu theo Danh mục sản phẩm
Mục tiêu: Tìm ra nhóm sản phẩm chủ lực
*/
SELECT 
    PC.Name AS 'CategoryName',
    FORMAT(SUM(SOD.LineTotal), 'N2') AS 'CategoryRevenue',
    CAST(SUM(SOD.LineTotal) * 100.0 / SUM(SUM(SOD.LineTotal)) OVER() AS DECIMAL(10,2)) AS 'RevenuePercentage'
FROM Sales.SalesOrderDetail AS SOD
JOIN Production.Product AS P ON SOD.ProductID = P.ProductID
JOIN Production.ProductSubcategory AS PS ON P.ProductSubcategoryID = PS.ProductSubcategoryID
JOIN Production.ProductCategory AS PC ON PS.ProductCategoryID = PC.ProductCategoryID
GROUP BY PC.Name
ORDER BY SUM(SOD.LineTotal) DESC;

/* TASK 3: Bảng xếp hạng Hiệu suất Nhân viên Bán hàng từ trước đến nay 
   Mục tiêu: Đánh giá nhân sự dựa trên tổng giá trị họ mang về cho công ty từ trước đến nay 
   và tỷ trọng đóng góp của họ vào tổng doanh thu toàn công ty.
*/
 -- Tính tổng tiền từng nhân viên đạt được 
WITH SalesRepHistory AS (
    SELECT 
        SOH.SalesPersonID,
        CONCAT(P.FirstName, ' ', P.LastName) AS 'SalesPersonName',
        COUNT(SOH.SalesOrderID) AS 'TotalOrders',
        SUM(SOH.SubTotal) AS 'AllTimeRevenue'
    FROM Sales.SalesOrderHeader AS SOH
    JOIN Person.Person AS P ON SOH.SalesPersonID = P.BusinessEntityID
    WHERE SOH.[Status] = 5 
      AND SOH.SalesPersonID IS NOT NULL -- Chỉ lấy những đơn hàng có nhân viên Sales phụ trách
    GROUP BY SOH.SalesPersonID, P.FirstName, P.LastName
)
-- Tính toán tỷ lệ đóng góp và Xếp hạng
SELECT 
    SalesPersonName,
    TotalOrders,
    FORMAT([AllTimeRevenue], 'N2') AS [AllTimeRevenue],
    
    -- Tính % đóng góp của nhân viên đó vào tổng doanh thu lịch sử của công ty
    CAST(([AllTimeRevenue] * 100.0 / SUM([AllTimeRevenue]) OVER()) AS DECIMAL(10,2)) AS 'Contribution_Pct',
    
    RANK() OVER(ORDER BY [AllTimeRevenue] DESC) AS 'PerformanceRank' 
FROM SalesRepHistory
ORDER BY PerformanceRank;

/* TASK 4: Phân tích Biên Lợi nhuận gộp & Phân loại hiệu suất Vùng
   Mục tiêu: Phân nhóm các vùng kinh doanh theo tỷ suất sinh lời.
*/

WITH HeaderStats AS (
    SELECT 
        TerritoryID,
        SUM(SubTotal) AS 'TotalRevenue' 
    FROM Sales.SalesOrderHeader
    WHERE [Status] = 5 
    GROUP BY TerritoryID
),
CostStats AS (
    SELECT 
        SOH.TerritoryID,
        SUM(SOD.OrderQty * PCH.StandardCost) AS 'TotalCOGS'
    FROM Sales.SalesOrderHeader AS SOH
    JOIN Sales.SalesOrderDetail AS SOD ON SOH.SalesOrderID = SOD.SalesOrderID
    JOIN Production.ProductCostHistory AS PCH 
        ON SOD.ProductID = PCH.ProductID 
        AND SOH.OrderDate >= PCH.StartDate 
        AND (SOH.OrderDate <= PCH.EndDate OR PCH.EndDate IS NULL)
    WHERE SOH.[Status] = 5 
    GROUP BY SOH.TerritoryID
),
FinalStats AS (
    SELECT 
        ST.[Name] AS [TerritoryName],
        ST.[Group] AS [Region],
        H.[TotalRevenue],
        C.[TotalCOGS],
        -- Tính % Margin 
        (H.[TotalRevenue] - C.[TotalCOGS]) * 100.0 / NULLIF(H.[TotalRevenue], 0) AS [MarginRaw]
    FROM Sales.SalesTerritory AS ST
    JOIN HeaderStats AS H ON ST.TerritoryID = H.TerritoryID
    JOIN CostStats AS C ON ST.TerritoryID = C.TerritoryID
)

SELECT 
    TerritoryName,
    Region,
    FORMAT(TotalRevenue, 'N2') AS [Revenue],
    FORMAT(MarginRaw, 'N2') + '%' AS [GrossMargin],
    
    CASE 
        WHEN [MarginRaw] >= 20 THEN 'High Performance'
        WHEN [MarginRaw] >= 10 AND [MarginRaw] < 20 THEN 'Average Performance'
        WHEN [MarginRaw] < 10 THEN 'Low Performance (Review Required)'
        ELSE 'No Data'
    END AS [Profitability_Status]

FROM FinalStats
ORDER BY MarginRaw DESC;

--- PHẦN III: PHÂN TÍCH HÀNH VI KHÁCH HÀNG 
/* TASK 5: Phân tích chu kỳ mua sắm của khách hàng */

WITH PurchaseHistory AS (
    SELECT 
        CustomerID,
        SalesOrderID,
        OrderDate, 
        -- Lấy ngày của đơn hàng liền trước của cùng 1 khách hàng
        LAG(OrderDate) OVER (PARTITION BY CustomerID ORDER BY OrderDate) AS 'PreviousOrderDate'
    FROM Sales.SalesOrderHeader
    WHERE [Status] = 5 
)

SELECT 
    CustomerID,
    COUNT(SalesOrderID) AS [TotalOrders], 
    
    /* Tính toán trung bình khoảng cách ngày */
    CAST(AVG(CAST(DATEDIFF(day, [PreviousOrderDate], OrderDate) AS DECIMAL(10,2))) AS DECIMAL(10,2)) AS 'AvgDaysBetweenOrders' 

FROM PurchaseHistory
WHERE PreviousOrderDate IS NOT NULL -- Bỏ qua đơn hàng đầu tiên (vì không có đơn trước đó để so sánh)
GROUP BY CustomerID
HAVING COUNT(SalesOrderID) > 1 -- Chỉ tập trung vào nhóm khách hàng có hành vi tái mua sắm
ORDER BY AvgDaysBetweenOrders ASC; 

/* TASK 6: Khám phá các cặp sản phẩm thường được mua cùng nhau (Market Basket)
   Kỹ thuật: Self-Join trên cùng một mã đơn hàng (SalesOrderID)
*/

SELECT TOP 20
    P1.[Name] AS [Product_A],
    P2.[Name] AS [Product_B],
    COUNT(*) AS [Combination_Count] -- Số lần mua cùng lúc 2 sản phẩm 'Product A' và 'Product B' trong tổng số đơn hàng từ trước đến nay 

FROM Sales.SalesOrderDetail AS SOD1
JOIN Sales.SalesOrderDetail AS SOD2 
    ON SOD1.SalesOrderID = SOD2.SalesOrderID 
    AND SOD1.ProductID < SOD2.ProductID 

JOIN Production.Product AS P1 ON SOD1.ProductID = P1.ProductID
JOIN Production.Product AS P2 ON SOD2.ProductID = P2.ProductID
JOIN Sales.SalesOrderHeader AS SOH ON SOD1.SalesOrderID = SOH.SalesOrderID

WHERE SOH.[Status] = 5 
GROUP BY P1.[Name], P2.[Name]
ORDER BY Combination_Count DESC;

/* TASK 7: Phân khúc khách hàng bằng mô hình RFM (Recency - Frequency - Monetary)
   Mục tiêu: Chân dung hóa khách hàng để tối ưu hóa chiến lược Marketing và CRM.
*/

-- BƯỚC 1: Tính toán 3 chỉ số R, F, M thô cho từng khách hàng
WITH RawRFM AS (
    SELECT 
        CustomerID,
        -- Recency (R): Số ngày từ lần mua cuối cùng đến mốc chốt dữ liệu (01/07/2014)
        DATEDIFF(day, MAX(OrderDate), '2014-07-01') AS [Recency_Days],
        -- Frequency (F): Tổng số đơn hàng thành công
        COUNT(SalesOrderID) AS [Frequency_Count],
        -- Monetary (M): Tổng giá trị chi tiêu
        SUM(SubTotal) AS [Monetary_Value]
    FROM Sales.SalesOrderHeader
    WHERE [Status] = 5
    GROUP BY CustomerID
),

-- BƯỚC 2: Chấm điểm khách hàng từ 1 đến 5 
RFM_Scoring AS (
    SELECT 
        CustomerID,
        [Recency_Days],
        [Frequency_Count],
        [Monetary_Value],
        
        -- R: Mua càng gần đây (số ngày NHỎ) thì điểm càng CAO (5). 
        NTILE(5) OVER (ORDER BY [Recency_Days] DESC) AS [R_Score],
        
        -- F: Số lần mua hàng càng lớn thì điểm càng cao (5) 
        NTILE(5) OVER (ORDER BY [Frequency_Count] ASC) AS [F_Score],

        -- M: Tổng giá trị mua hàng càng lớn thì điểm càng cao (5) 
        NTILE(5) OVER (ORDER BY [Monetary_Value] ASC) AS [M_Score]
    FROM RawRFM
)

-- BƯỚC 3: Tổng hợp điểm số và dán nhãn phân khúc (Segmentation)
SELECT 
    CustomerID,
    Recency_Days,
    Frequency_Count,
    FORMAT([Monetary_Value], 'N2') AS [Monetary_Value],
    CONCAT([R_Score], [F_Score], [M_Score]) AS [RFM_Score],
    
    CASE 
        WHEN CONCAT([R_Score], [F_Score], [M_Score]) IN ('555', '554', '545', '455', '454') THEN 'Champions (VVIP)'
        WHEN [R_Score] >= 4 AND [F_Score] >= 3 THEN 'Loyal Customers'
        WHEN [R_Score] >= 4 AND [F_Score] <= 2 THEN 'New / Promising'
        WHEN [R_Score] <= 2 AND [M_Score] >= 4 THEN 'At Risk (Big Spenders)'
        WHEN [R_Score] <= 2 AND [F_Score] <= 2 THEN 'Lost Customers'
        ELSE 'General / Average'
    END AS Customer_Segment

FROM RFM_Scoring
ORDER BY RFM_Score DESC, Monetary_Value DESC;
