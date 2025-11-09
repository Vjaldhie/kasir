# kasir
kasir-app
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplikasi Kasir</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        body { background-color: #f8f9fa; }
        .card { margin-bottom: 20px; }
        .total { font-weight: bold; color: #28a745; }
        .alert { display: none; }
    </style>
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
        <div class="container">
            <a class="navbar-brand" href="#"><i class="fas fa-cash-register"></i> Kasir App</a>
            <div class="navbar-nav">
                <a class="nav-link active" href="#products">Produk</a>
                <a class="nav-link" href="#transactions">Transaksi</a>
                <a class="nav-link" href="#reports">Laporan</a>
            </div>
        </div>
    </nav>

    <div class="container mt-4">
        <!-- Produk Section -->
        <div id="products" class="card">
            <div class="card-header bg-success text-white">
                <h5><i class="fas fa-box"></i> Manajemen Produk</h5>
            </div>
            <div class="card-body">
                <form id="productForm" class="mb-3">
                    <div class="row">
                        <div class="col-md-3"><input type="text" id="productName" class="form-control" placeholder="Nama Produk" required></div>
                        <div class="col-md-3"><input type="number" id="productPrice" class="form-control" placeholder="Harga" required></div>
                        <div class="col-md-3"><input type="number" id="productStock" class="form-control" placeholder="Stok" required></div>
                        <div class="col-md-3"><button type="submit" class="btn btn-primary w-100"><i class="fas fa-plus"></i> Tambah</button></div>
                    </div>
                </form>
                <table class="table table-striped" id="productTable">
                    <thead>
                        <tr><th>Nama</th><th>Harga</th><th>Stok</th><th>Aksi</th></tr>
                    </thead>
                    <tbody></tbody>
                </table>
            </div>
        </div>

        <!-- Transaksi Section -->
        <div id="transactions" class="card">
            <div class="card-header bg-warning text-dark">
                <h5><i class="fas fa-shopping-cart"></i> Transaksi</h5>
            </div>
            <div class="card-body">
                <div class="row">
                    <div class="col-md-6">
                        <select id="productSelect" class="form-select mb-3">
                            <option value="">Pilih Produk</option>
                        </select>
                        <input type="number" id="quantity" class="form-control mb-3" placeholder="Jumlah" min="1">
                        <button id="addToCart" class="btn btn-success"><i class="fas fa-cart-plus"></i> Tambah ke Keranjang</button>
                    </div>
                    <div class="col-md-6">
                        <h6>Keranjang:</h6>
                        <ul id="cartList" class="list-group mb-3"></ul>
                        <p class="total">Total: Rp <span id="total">0</span></p>
                        <button id="checkout" class="btn btn-primary"><i class="fas fa-check"></i> Checkout</button>
                    </div>
                </div>
                <div id="alert" class="alert alert-success mt-3">Transaksi berhasil!</div>
            </div>
        </div>

        <!-- Laporan Section -->
        <div id="reports" class="card">
            <div class="card-header bg-info text-white">
                <h5><i class="fas fa-chart-line"></i> Laporan Transaksi</h5>
            </div>
            <div class="card-body">
                <table class="table table-striped" id="reportTable">
                    <thead>
                        <tr><th>Tanggal</th><th>Produk</th><th>Jumlah</th><th>Total</th></tr>
                    </thead>
                    <tbody></tbody>
                </table>
                <p class="total">Total Penjualan: Rp <span id="totalSales">0</span></p>
            </div>
        </div>
    </div>

    <script>
        // Load data from localStorage
        let products = JSON.parse(localStorage.getItem('products')) || [];
        let transactions = JSON.parse(localStorage.getItem('transactions')) || [];
        let cart = [];

        // Render Products
        function renderProducts() {
            const tbody = document.querySelector('#productTable tbody');
            tbody.innerHTML = '';
            products.forEach((p, i) => {
                tbody.innerHTML += `<tr><td>${p.name}</td><td>Rp ${p.price}</td><td>${p.stock}</td><td><button class="btn btn-danger btn-sm" onclick="deleteProduct(${i})"><i class="fas fa-trash"></i></button></td></tr>`;
            });
            updateProductSelect();
        }

        // Add Product
        document.getElementById('productForm').addEventListener('submit', (e) => {
            e.preventDefault();
            const name = document.getElementById('productName').value;
            const price = parseInt(document.getElementById('productPrice').value);
            const stock = parseInt(document.getElementById('productStock').value);
            products.push({ name, price, stock });
            localStorage.setItem('products', JSON.stringify(products));
            renderProducts();
            e.target.reset();
        });

        // Delete Product
        function deleteProduct(index) {
            products.splice(index, 1);
            localStorage.setItem('products', JSON.stringify(products));
            renderProducts();
        }

        // Update Product Select
        function updateProductSelect() {
            const select = document.getElementById('productSelect');
            select.innerHTML = '<option value="">Pilih Produk</option>';
            products.forEach((p, i) => {
                select.innerHTML += `<option value="${i}">${p.name} (Stok: ${p.stock})</option>`;
            });
        }

        // Add to Cart
        document.getElementById('addToCart').addEventListener('click', () => {
            const index = document.getElementById('productSelect').value;
            const qty = parseInt(document.getElementById('quantity').value);
            if (index !== '' && qty > 0 && qty <= products[index].stock) {
                cart.push({ index: parseInt(index), qty });
                renderCart();
            }
        });

        // Render Cart
        function renderCart() {
            const list = document.getElementById('cartList');
            list.innerHTML = '';
            let total = 0;
            cart.forEach((c, i) => {
                const p = products[c.index];
                const subtotal = p.price * c.qty;
                total += subtotal;
                list.innerHTML += `<li class="list-group-item d-flex justify-content-between">${p.name} x${c.qty} <span>Rp ${subtotal}</span></li>`;
            });
            document.getElementById('total').textContent = total;
        }

        // Checkout
        document.getElementById('checkout').addEventListener('click', () => {
            if (cart.length > 0) {
                cart.forEach(c => {
                    products[c.index].stock -= c.qty;
                });
                transactions.push({ date: new Date().toLocaleString(), items: cart, total: parseInt(document.getElementById('total').textContent) });
                localStorage.setItem('products', JSON.stringify(products));
                localStorage.setItem('transactions', JSON.stringify(transactions));
                cart = [];
                renderCart();
                renderProducts();
                renderReports();
                document.getElementById('alert').style.display = 'block';
                setTimeout(() => document.getElementById('alert').style.display = 'none', 2000);
            }
        });

        // Render Reports
        function renderReports() {
            const tbody = document.querySelector('#reportTable tbody');
            tbody.innerHTML = '';
            let totalSales = 0;
            transactions.forEach(t => {
                totalSales += t.total;
                t.items.forEach(item => {
                    const p = products[item.index];
                    tbody.innerHTML += `<tr><td>${t.date}</td><td>${p.name}</td><td>${item.qty}</td><td>Rp ${p.price * item.qty}</td></tr>`;
                });
            });
            document.getElementById('totalSales').textContent = totalSales;
        }

        // Initial Render
        renderProducts();
        renderReports();
    </script>
</body>
</html>
