### Amana Bookstore – Project Q&A (1–16)

#### 1) Main Welcome (Hero)

- File: `src/app/page.tsx`
- The hero with the welcome headline is rendered on the home page.

```15:23:Amana-Bookstore/src/app/page.tsx
  return (
    <div className="container mx-auto px-4 py-8">
      {/* Welcome Section */}
      <section className="text-center bg-blue-100 p-8 rounded-lg mb-12 shadow-md">
        <h1 className="text-4xl font-extrabold text-gray-800 mb-2">Welcome to the Amana Bookstore!</h1>
        <p className="text-lg text-gray-600">
          Your one-stop shop for the best books. Discover new worlds and adventures.
        </p>
      </section>
```

#### 2) Data – Books Source

- File: `src/app/data/books.ts`
- All book objects are exported from the `books` array.

```1:6:Amana-Bookstore/src/app/data/books.ts
// src/app/data/books.ts
import { Book } from '../types';

export const books: Book[] = [
  // Physics Textbooks
```

#### 3) Navbar

- File: `src/app/components/Navbar.tsx`
- Controls the top navigation (brand, Home, My Cart, badge).

```46:56:Amana-Bookstore/src/app/components/Navbar.tsx
  return (
    <nav className="bg-white shadow-md fixed w-full top-0 z-10">
      <div className="container mx-auto px-6 py-3 flex justify-between items-center">
        <Link href="/" className="text-2xl font-bold text-gray-800 cursor-pointer">
          Amana Bookstore
        </Link>
        <div className="flex items-center space-x-4">
          <Link href="/" className={`text-gray-600 hover:text-blue-500 cursor-pointer ${pathname === '/' ? 'text-blue-500 font-semibold' : ''}`}>
            Home
          </Link>
          <Link href="/cart" className={`text-gray-600 hover:text-blue-500 flex items-center cursor-pointer ${pathname === '/cart' ? 'text-blue-500 font-semibold' : ''}`}>
            My Cart
```

#### 4) Components – Purpose and References

- `BookCard.tsx`: Featured card for a book (used in Featured section). Referenced by `BookGrid.tsx`.
- `BookListItem.tsx`: Compact row for list view. Referenced by `BookGrid.tsx`.
- `BookGrid.tsx`: Catalog UI (featured carousel, search, filter, sort, pagination). Used by `page.tsx`.
- `CartItem.tsx`: Single item in cart page. Used by `cart/page.tsx`.
- `Navbar.tsx`: Global navigation. Used by `layout.tsx`.
- `Pagination.tsx`: Paging controls. Used by `BookGrid.tsx`.

#### 5) Book Displays – Featured vs All Books

- All Books section uses `BookListItem`:

```283:289:Amana-Bookstore/src/app/components/BookGrid.tsx
        {filteredAndSortedBooks.length > 0 ? (
          <>
            <div className="space-y-3">
              {paginatedBooks.map(book => (
                <BookListItem key={book.id} book={book} onAddToCart={onAddToCart} />
              ))}
```

- Featured section uses `BookCard`:

```182:188:Amana-Bookstore/src/app/components/BookGrid.tsx
        {/* Featured Books Carousel */}
        <div className="relative">
          <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-8">
            {currentFeaturedBooks.map(book => (
              <BookCard key={book.id} book={book} onAddToCart={onAddToCart} />
            ))}
```

#### 6) Book Properties in Featured Cards

- Flow: `books` data → `page.tsx` → `BookGrid` → `BookCard` props.

```95:123:Amana-Bookstore/src/app/components/BookCard.tsx
      {/* Book Information */}
      <div className="p-4">
        <Link href={`/book/${book.id}`} className="block cursor-pointer">
          <h3 className="text-lg font-semibold text-gray-800 truncate hover:text-blue-600 transition-colors duration-200">{book.title}</h3>
          <p className="text-sm text-gray-600 mt-1">by {book.author}</p>
        </Link>
        <div className="flex items-center mt-2">
          {renderStars(book.rating)}
          <span className="text-xs text-gray-500 ml-2">({book.reviewCount} reviews)</span>
        </div>
        <div className="mt-2">
          {book.genre.slice(0, 2).map((g) => (
            <span key={g} className="inline-block bg-gray-200 rounded-full px-2 py-1 text-xs font-semibold text-gray-700 mr-2 mb-2">
              {g}
            </span>
          ))}
        </div>
        <div className="flex items-center justify-between mt-3">
          <p className="text-xl font-bold text-gray-900">${book.price.toFixed(2)}</p>
```

#### 7) URL Routing – Dynamic Book Pages

- Next.js dynamic route: `src/app/book/[id]/page.tsx` generates `/book/<id>`.

```18:25:Amana-Bookstore/src/app/book/[id]/page.tsx
  const params = useParams();
  const router = useRouter();
  const { id } = params;

  useEffect(() => {
    if (id) {
      const foundBook = books.find((b) => b.id === id);
```

#### 8) Cart Management – Where and How

- Client-side `localStorage` with a custom `cartUpdated` event.
  - Add to cart (book detail):

```37:70:Amana-Bookstore/src/app/book/[id]/page.tsx
  const handleAddToCart = () => {
    if (!book) return;
    const storedCart = localStorage.getItem('cart');
    const cart: CartItem[] = storedCart ? JSON.parse(storedCart) : [];
    const existingItemIndex = cart.findIndex((item) => item.bookId === book.id);
    if (existingItemIndex > -1) cart[existingItemIndex].quantity += quantity; else cart.push({ id: `${book.id}-${Date.now()}`, bookId: book.id, quantity, addedAt: new Date().toISOString() });
    localStorage.setItem('cart', JSON.stringify(cart));
    window.dispatchEvent(new CustomEvent('cartUpdated'));
    router.push('/cart');
  };
```

- Navbar badge listener:

```14:23:Amana-Bookstore/src/app/components/Navbar.tsx
  useEffect(() => {
    const updateCartCount = () => {
      const storedCart = localStorage.getItem('cart');
      if (storedCart) {
        try {
          const cart: CartItem[] = JSON.parse(storedCart);
          const count = cart.reduce((total, item) => total + item.quantity, 0);
          setCartItemCount(count);
```

- Cart page load/update/remove/clear: `src/app/cart/page.tsx`.

#### 9) Adding Books – How and Proof

- Add a new object to `src/app/data/books.ts` with a unique `id`.
- Example added (id `46`):

```872:878:Amana-Bookstore/src/app/data/books.ts
  {
    id: '46',
    title: 'Data Visualization with D3 and React',
    author: 'Eng. Noor Al-Hassan',
    ...
    featured: false,
  }
```

- Verify by visiting `/book/46` or searching on the home page.

Full details of the added book (`id: 46`):

```879:897:Amana-Bookstore/src/app/data/books.ts
  {
    id: '46',
    title: 'Data Visualization with D3 and React',
    author: 'Eng. Noor Al-Hassan',
    description:
      'Practical guide to building interactive data visualizations on the web using D3.js integrated with modern React patterns.',
    price: 84.5,
    image: '/images/book4.jpg',
    isbn: '978-4678901234',
    genre: ['Computer Science', 'Data Visualization'],
    tags: ['D3.js', 'React', 'Frontend'],
    datePublished: '2024-02-11',
    pages: 432,
    language: 'English',
    publisher: 'Al-Jazari Web Systems',
    rating: 4.4,
    reviewCount: 5,
    inStock: true,
    featured: false,
  },
```

#### 10) Filtering – Featured and Category

- Featured: boolean `featured` flag.

```24:26:Amana-Bookstore/src/app/components/BookGrid.tsx
  const featuredBooks = useMemo(() => books.filter(book => book.featured), [books]);
```

- Category (genre) + search filter:

```61:73:Amana-Bookstore/src/app/components/BookGrid.tsx
  const filteredAndSortedBooks = useMemo(() => {
    const filtered = books.filter(book => {
      const matchesSearch = book.title.toLowerCase().includes(searchQuery.toLowerCase()) || book.author.toLowerCase().includes(searchQuery.toLowerCase());
      const matchesGenre = selectedGenre === 'All' || book.genre.includes(selectedGenre);
      return matchesSearch && matchesGenre;
    });
```

#### 11) Sorting – User Selection

- Switch on `sortBy` and flip by `sortOrder`.

```75:106:Amana-Bookstore/src/app/components/BookGrid.tsx
    const sorted = [...filtered].sort((a, b) => {
      let comparison = 0;
      switch (sortBy) {
        case 'title': comparison = a.title.localeCompare(b.title); break;
        case 'author': comparison = a.author.localeCompare(b.author); break;
        case 'datePublished': comparison = new Date(a.datePublished).getTime() - new Date(b.datePublished).getTime(); break;
        case 'rating': comparison = a.rating - b.rating; break;
        case 'reviewCount': comparison = a.reviewCount - b.reviewCount; break;
        case 'price': comparison = a.price - b.price; break;
        default: comparison = 0;
      }
      return sortOrder === 'asc' ? comparison : -comparison;
    });
```

#### 12) Pagination – Mechanics and Items Per Page

- Slice by `currentPage` and `itemsPerPage`; compute `totalPages`.

```108:116:Amana-Bookstore/src/app/components/BookGrid.tsx
  const paginatedBooks = useMemo(() => {
    const startIndex = (currentPage - 1) * itemsPerPage;
    const endIndex = startIndex + itemsPerPage;
    return filteredAndSortedBooks.slice(startIndex, endIndex);
  }, [filteredAndSortedBooks, currentPage, itemsPerPage]);

  const totalPages = Math.ceil(filteredAndSortedBooks.length / itemsPerPage);
```

- Change items per page: initial state in `BookGrid` and selector in `Pagination`.

```20:22:Amana-Bookstore/src/app/components/BookGrid.tsx
  const [itemsPerPage, setItemsPerPage] = useState(8);
```

```134:151:Amana-Bookstore/src/app/components/Pagination.tsx
      <div className="flex items-center space-x-2 text-sm text-gray-600">
        <span>Items per page:</span>
        <select value={itemsPerPage} ...>
          <option value={8}>8</option>
          <option value={12}>12</option>
          <option value={16}>16</option>
          <option value={24}>24</option>
        </select>
      </div>
```

#### 13) Styling – Colors and Hero Text Size

- Styling uses Tailwind utilities (plus minimal `globals.css`).
- Change button blue: edit class strings like `bg-blue-600 hover:bg-blue-700` inside components (e.g., `BookCard`, `BookListItem`, detail page).

```132:143:Amana-Bookstore/src/app/components/BookCard.tsx
  className={`... ${
    ... : 'bg-blue-600 text-white hover:bg-blue-700 cursor-pointer'
  }`}
```

- Change hero text size: update `text-4xl` in `page.tsx` to `text-5xl`/`text-6xl` etc.

```18:21:Amana-Bookstore/src/app/page.tsx
  <section className="text-center bg-blue-100 p-8 rounded-lg mb-12 shadow-md">
    <h1 className="text-4xl font-extrabold text-gray-800 mb-2">Welcome to the Amana Bookstore!</h1>
```

#### 14) Typesetting – `types/index.ts`

- Central TypeScript interfaces for `Book`, `CartItem`, `Review`. Imported across components to type props/state and ensure data consistency.

```3:13:Amana-Bookstore/src/app/types/index.ts
export interface Book {
  id: string;
  title: string;
  author: string;
  description: string;
  price: number;
  image: string;
  isbn: string;
  genre: string[];
  tags: string[];
```

#### 15) Dependencies – `package.json`

- Declares project metadata, scripts, and dependencies for install/build/run.

```1:16:Amana-Bookstore/package.json
{
  "name": "amana-bookstore",
  "scripts": { "dev": "next dev", "build": "next build", "start": "next start", "lint": "eslint" },
  "dependencies": { "react": "19.1.0", "react-dom": "19.1.0", "next": "15.5.0" }
```

#### 16) Public Files – `public/` vs `app/`

- `public/`: static assets served as-is at the site root, e.g., `public/images/book1.jpg` referenced as `/images/book1.jpg` from code.
- `app/`: application code for routes, components, and logic (Next.js App Router).

---

### Site Customizations (UI Tweaks Implemented)

We made five minor, cohesive style updates to polish the UI. Each item below includes a short description and a code reference to the exact edit.

1. Hero section restyle (background tint and larger heading)

```16:23:Amana-Bookstore/src/app/page.tsx
    <div className="container mx-auto px-4 py-8">
      {/* Welcome Section */}
      <section className="text-center bg-indigo-50 p-10 rounded-xl mb-12 shadow-md">
        <h1 className="text-5xl font-extrabold text-gray-900 tracking-tight mb-3">
          Welcome to the Amana Bookstore!
        </h1>
        <p className="text-xl text-gray-600">
```

2. Primary buttons: blue → indigo (cards and list items)

```132:144:Amana-Bookstore/src/app/components/BookCard.tsx
                : isAddingToCart
                ? 'bg-indigo-400 text-white cursor-wait'
                : 'bg-indigo-600 text-white hover:bg-indigo-700 cursor-pointer'
```

```159:167:Amana-Bookstore/src/app/components/BookListItem.tsx
                      ? 'bg-indigo-400 text-white cursor-wait'
                      : 'bg-indigo-600 text-white hover:bg-indigo-700 cursor-pointer'
```

3. Navbar active/hover color and badge: blue → indigo

```52:63:Amana-Bookstore/src/app/components/Navbar.tsx
          <Link href="/" className={`text-gray-600 hover:text-indigo-600 cursor-pointer ${pathname === '/' ? 'text-indigo-600 font-semibold' : ''}`}>
            Home
          </Link>
          <Link href="/cart" className={`text-gray-600 hover:text-indigo-600 flex items-center cursor-pointer ${pathname === '/cart' ? 'text-indigo-600 font-semibold' : ''}`}>
            My Cart
            {cartItemCount > 0 && (
              <span className="ml-2 bg-indigo-600 text-white text-xs font-bold rounded-full h-5 w-5 flex items-center justify-center">
```

4. Pagination active state: blue → indigo

```102:109:Amana-Bookstore/src/app/components/Pagination.tsx
                  className={`px-3 py-2 rounded-lg border text-sm font-medium transition-colors cursor-pointer ${
                    currentPage === page
                      ? 'bg-indigo-600 text-white border-indigo-600'
                      : 'bg-white text-gray-700 border-gray-300 hover:bg-gray-50 hover:border-gray-400'
                  }`}
```

5. Featured section subtitle for context

```199:205:Amana-Bookstore/src/app/components/BookGrid.tsx
        </div>
        <p className="text-gray-500 mb-6">Hand-picked highlights from our catalog</p>
```

Impact summary

- Unified accent color (indigo) across buttons, navbar, and pagination.
- More prominent, welcoming hero area.
- Featured section has a brief explanatory subtitle.
