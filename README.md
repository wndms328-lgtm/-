## 요구사항

### 기본
- [x] 기본 항목 1
- [ ] 기본 항목 2

# // ArticleService.js

# // Article API 요청 함수들을 구현합니다.

# // https://panda-market-api-crud.vercel.app/docs 의 Article API를 사용합니다.

import axios from 'axios';

const BASE = 'https://panda-market-api-crud.vercel.app/articles';

function okOrThrow(res, action) {

if (res.status >= 200 && res.status < 300) {
return res.data;
}

const msg = `[Article API Error] ${action} -> ${res.status} ${res.statusText}`;

console.error(msg);
throw new Error(msg);
}

export function getArticleList(page = 1, pageSize = 10, keyword = '') {

try {
if (typeof page !== 'number' || page < 1) throw new Error('page must be a positive number');
if (typeof pageSize !== 'number' || pageSize < 1) throw new Error('pageSize must be a positive number');
if (typeof keyword !== 'string') throw new Error('keyword must be a string');

    console.log(
      `[Article API] GET ${BASE} - page: ${page}, pageSize: ${pageSize}, keyword: "${keyword}"`
    );

    return axios
      .get(`${BASE}`, {
        params: { page, pageSize, keyword },
        headers: { Accept: 'application/json' },
      })
      .then((res) => okOrThrow(res, 'getArticleList'))
      .catch((err) => {
        console.error('getArticleList() failed:', err.message);
        throw err;
      });

} catch (error) {
console.error('getArticleList() parameter validation failed:', error.message);
throw error;
}
}

export function getArticle(id) {
try {
if (!id || (typeof id !== 'number' && typeof id !== 'string')) {
throw new Error('id must be a valid number or string');
}

    const url = `${BASE}/${id}`;
    console.log(`[Article API] GET ${url}`);

    return axios
      .get(url, { headers: { Accept: 'application/json' } })
      .then((res) => okOrThrow(res, 'getArticle'))
      .catch((err) => {
        console.error('getArticle() failed:', err.message);
        throw err;
      });

} catch (error) {
console.error('getArticle() parameter validation failed:', error.message);
throw error;
}
}

export function createArticle({ title, content, image }) {
try {
if (!title || typeof title !== 'string') throw new Error('title is required and must be a string');
if (!content || typeof content !== 'string') throw new Error('content is required and must be a string');
if (image !== undefined && typeof image !== 'string') throw new Error('image must be a string if provided');

    const requestBody = { title, content, ...(image && { image }) };

    console.log(`[Article API] POST ${BASE}`, requestBody);

    return axios
      .post(BASE, requestBody, {
        headers: {
          'Content-Type': 'application/json',
          Accept: 'application/json',
        },
      })
      .then((res) => okOrThrow(res, 'createArticle'))
      .catch((err) => {
        console.error('createArticle() failed:', err.message);
        throw err;
      });

} catch (error) {
console.error('createArticle() parameter validation failed:', error.message);
throw error;
}
}

export function patchArticle(id, partial) {
try {
if (!id || (typeof id !== 'number' && typeof id !== 'string')) {
throw new Error('id must be a valid number or string');
}
if (!partial || typeof partial !== 'object') {
throw new Error('partial must be an object');
}

    const url = `${BASE}/${id}`;
    console.log(`[Article API] PATCH ${url}`, partial);

    return axios
      .patch(url, partial, {
        headers: {
          'Content-Type': 'application/json',
          Accept: 'application/json',
        },
      })
      .then((res) => okOrThrow(res, 'patchArticle'))
      .catch((err) => {
        console.error('patchArticle() failed:', err.message);
        throw err;
      });

} catch (error) {
console.error('patchArticle() parameter validation failed:', error.message);
throw error;
}
}

export function deleteArticle(id) {
try {
if (!id || (typeof id !== 'number' && typeof id !== 'string')) {
throw new Error('id must be a valid number or string');
}

    const url = `${BASE}/${id}`;
    console.log(`[Article API] DELETE ${url}`);

    return axios
      .delete(url, { headers: { Accept: 'application/json' } })
      .then((res) => okOrThrow(res, 'deleteArticle'))
      .catch((err) => {
        console.error('deleteArticle() failed:', err.message);
        throw err;
      });

} catch (error) {
console.error('deleteArticle() parameter validation failed:', error.message);
throw error;
}
}

# // 데모용 생성 함수
export function createTravelArticle() {
try {
const articleData = {
image: 'https://example.com/...',
content: '공항에서 설렘가득 담고.',
title: '여행 떠나기 전 공항에서.',
// 참고: Article API 스키마에 없는 필드를 넣으면 400이 날 수 있음
// price, tags, favoriteCount 등은 Article 엔드포인트에서 지원하지 않을 수 있습니다.
};

    console.log('[Article API] 여행 글 생성 시작...');
    return createArticle(articleData);

} catch (err) {
console.error('createTravelArticle() failed:', err.message);
throw err;
}
}


# // ProductService.js
# // Product API 요청 함수들을 구현합니다.
# // https://panda-market-api-crud.vercel.app/docs 의 Product API를 사용합니다.

const BASE = 'https://panda-market-api-crud.vercel.app/products';

async function parseOrThrow(res, label) {
  if (res.ok) {
    try {
      return await res.json();
    } catch (parseError) {
      const msg = `${label} failed: JSON parsing error`;
      console.error('[Product API Error]', msg, parseError);
      throw new Error(msg);
    }
  }

  let msg = `${label} failed (HTTP ${res.status} ${res.statusText})`;
  try {
    const errorData = await res.json();
    msg = errorData?.message || errorData?.error || msg;
  } catch (parseError) {
   # // JSON 파싱 실패 시 기본 메시지 사용
  }

  console.error('[Product API Error]', msg);
  throw new Error(msg);
}

export async function getProductList(page = 1, pageSize = 10, keyword = '') {
  try {
    # // 파라미터 유효성 검사
    if (typeof page !== 'number' || page < 1) {
      throw new Error('page must be a positive number');
    }
    if (typeof pageSize !== 'number' || pageSize < 1) {
      throw new Error('pageSize must be a positive number');
    }
    if (typeof keyword !== 'string') {
      throw new Error('keyword must be a string');
    }

    # // URL 구성 - 쿼리 파라미터 사용
    const params = new URLSearchParams({
      page: page.toString(),
      pageSize: pageSize.toString(),
      keyword: keyword,
    });
    const url = `${BASE}?${params.toString()}`;

    console.log(`[Product API] GET ${url}`);
    const res = await fetch(url);
    return await parseOrThrow(res, 'getProductList');
  } catch (e) {
    console.error('getProductList() failed:', e.message);
    throw e;
  }
}

export async function getProduct(id) {
  try {
    // ID 유효성 검사
    if (!id || (typeof id !== 'number' && typeof id !== 'string')) {
      throw new Error('id must be a valid number or string');
    }

    const url = `${BASE}/${id}`;
    console.log(`[Product API] GET ${url}`);
    const res = await fetch(url);
    return await parseOrThrow(res, 'getProduct');
  } catch (e) {
    console.error('getProduct() failed:', e.message);
    throw e;
  }
}

export async function createProduct({
  name,
  description,
  price,
  tags = [],
  images = [],
}) {
  try {
    // 필수 필드 유효성 검사
    if (!name || typeof name !== 'string') {
      throw new Error('name is required and must be a string');
    }
    if (!description || typeof description !== 'string') {
      throw new Error('description is required and must be a string');
    }
    if (typeof price !== 'number' || price < 0) {
      throw new Error('price must be a non-negative number');
    }
    if (!Array.isArray(tags)) {
      throw new Error('tags must be an array');
    }
    if (!Array.isArray(images)) {
      throw new Error('images must be an array');
    }

    # // request body 구성
    const requestBody = {
      name,
      description,
      price,
      tags,
      images,
    };

    console.log(`[Product API] POST ${BASE}`, requestBody);
    const res = await fetch(BASE, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        Accept: 'application/json',
      },
      body: JSON.stringify(requestBody),
    });
    return await parseOrThrow(res, 'createProduct');
  } catch (e) {
    console.error('createProduct() failed:', e.message);
    throw e;
  }
}

export async function patchProduct(id, partial) {
  try {
    // ID 유효성 검사
    if (!id || (typeof id !== 'number' && typeof id !== 'string')) {
      throw new Error('id must be a valid number or string');
    }
    if (!partial || typeof partial !== 'object') {
      throw new Error('partial must be an object');
    }

    const url = `${BASE}/${id}`;
    console.log(`[Product API] PATCH ${url}`, partial);
    const res = await fetch(url, {
      method: 'PATCH',
      headers: {
        'Content-Type': 'application/json',
        Accept: 'application/json',
      },
      body: JSON.stringify(partial),
    });
    return await parseOrThrow(res, 'patchProduct');
  } catch (e) {
    console.error('patchProduct() failed:', e.message);
    throw e;
  }
}

export async function deleteProduct(id) {
  try {
    // ID 유효성 검사
    if (!id || (typeof id !== 'number' && typeof id !== 'string')) {
      throw new Error('id must be a valid number or string');
    }

    const url = `${BASE}/${id}`;
    console.log(`[Product API] DELETE ${url}`);
    const res = await fetch(url, {
      method: 'DELETE',
      headers: {
        Accept: 'application/json',
      },
    });
    return await parseOrThrow(res, 'deleteProduct');
  } catch (e) {
    console.error('deleteProduct() failed:', e.message);
    throw e;
  }
}

# // Bespoke AI 제트 400W 제품 생성 함수
export async function createBespokeAIJet() {
  const productData = {
    images: ['https://example.com/...'],
    tags: ['무선 청소기'],
    price: 599000,
    description: '삼성전자 Bespoke AI 제트 400W 무선 청소기',
    name: 'Bespoke AI 제트 400W',
    tags: ['무선청소기', '삼성전자', 'AI'],
    favoriteCount: 26,
  };

  try {
    return await createProduct(productData);
  } catch (e) {
    console.error('createBespokeAIJet() failed:', e.message);
    throw e;
  }
}


# // src/main.js
import util from 'node:util';
import {
  getArticleList,
  getArticle,
  createArticle,
  patchArticle,
  deleteArticle,
  createTravelArticle,
} from './ArticleService.js';
import {
  getProductList,
  getProduct,
  createProduct,
  patchProduct,
  deleteProduct,
  createBespokeAIJet,
} from './ProductService.js';

// 추상화/캡슐화: 프라이빗 필드(#)로 내부 상태 보호, 메서드로만 조작
class Product {
  #favoriteCount = 0;
  constructor(name, description, price, tags = [], images = []) {
    this.name = name;
    this.description = description;
    this.price = price;
    this.tags = tags;
    this.images = images;
  }
  get favoriteCount() {
    return this.#favoriteCount;
  }
  favorite() {
    this.#favoriteCount += 1;
  }
  // 다형성: 공통 요약
  summary() {
    return `[상품] ${this.name} - ${this.price}원`;
  }
}

class ElectronicProduct extends Product {
  constructor(
    name,
    description,
    price,
    tags = [],
    images = [],
    manufacturer = 'Unknown'
  ) {
    super(name, description, price, tags, images);
    this.manufacturer = manufacturer;
  }
  // 다형성: 오버라이드
  summary() {
    return `[전자제품] ${this.name} (${this.manufacturer}) - ${this.price}원`;
  }
}

class Article {
  #likeCount = 0;
  constructor(title, content, writer) {
    this.title = title;
    this.content = content;
    this.writer = writer;
    this.createdAt = new Date(); // 심화 요구사항
  }
  get likeCount() {
    return this.#likeCount;
  }
  like() {
    this.#likeCount += 1;
  }
}

/* ===========================
   실행 데모
   =========================== */
(async () => {
  // ------- Article API (.then/.catch) -------
  getArticleList(1, 5, '')
    .then((list) => console.log('Article List(meta):', list?.meta))
    .catch(() => {});

  getArticle(1)
    .then((art) =>
      console.log('Article #1:', { id: art?.id, title: art?.title })
    )
    .catch(() => {});

  // 여행 글 생성
  createTravelArticle()
    .then((travelArticle) => {
      console.log('Created Travel Article:', {
        id: travelArticle?.id,
        title: travelArticle?.title,
        content: travelArticle?.content,
        image: travelArticle?.image,
      });
    })
    .catch(() => {});

  createArticle({
    title: '도시 여행기',
    content: '내용입니다.',
    image: 'https://picsum.photos/600/400',
  })
    .then((created) => {
      console.log('Created Article:', {
        id: created?.id,
        title: created?.title,
      });

      // Article instance 생성 및 출력
      const articleInstance = new Article(
        created.title,
        '도시 여행 내용입니다.',
        '작성자'
      );
      articleInstance.like();
      console.log('Created Article instance:', {
        title: articleInstance.title,
        likeCount: articleInstance.likeCount,
        createdAt: articleInstance.createdAt.toISOString(),
      });

      return patchArticle(created.id, { title: '수정된 제목' });
    })
    .then((patched) => {
      console.log('Patched Article:', {
        id: patched?.id,
        title: patched?.title,
      });

      // 수정된 Article instance 생성 및 출력
      const patchedArticleInstance = new Article(
        patched.title,
        '수정된 내용',
        '작성자'
      );
      patchedArticleInstance.like();
      patchedArticleInstance.like();
      console.log('Patched Article instance:', {
        title: patchedArticleInstance.title,
        likeCount: patchedArticleInstance.likeCount,
        createdAt: patchedArticleInstance.createdAt.toISOString(),
      });

      return deleteArticle(patched.id);
    })
    .then((deleted) => {
      console.log('Deleted Article:', deleted?.id);

      // 삭제된 Article instance 생성 및 출력
      const deletedArticleInstance = new Article(
        '도심 속으로',
        '삭제된 내용',
        '작성자'
      );
      console.log('Deleted Article instance:', {
        title: deletedArticleInstance.title,
        likeCount: deletedArticleInstance.likeCount,
        createdAt: deletedArticleInstance.createdAt.toISOString(),
      });
    })
    .catch(() => {});

  # // ------- Product API (async/await) -------
  try {
    console.log('[Product API] 상품 목록 조회 시작...');
    const listRes = await getProductList(1, 10, '');
    const items = listRes?.list ?? [];
    console.log(`[Product API] ${items.length}개의 상품을 조회했습니다.`);

    // 요구사항: 리스트를 인스턴스로 변환해 products 배열에 저장
    // 해시태그에 "전자제품"이 포함되어 있는 상품들은 ElectronicProduct 클래스 사용
    // 나머지 상품들은 모두 Product 클래스 사용
    const products = items
      .map((productData, index) => {
        try {
          // 상품 데이터 유효성 검사
          if (!productData || typeof productData !== 'object') {
            console.warn(
              `[Product API] 상품 ${index}번째 데이터가 유효하지 않습니다.`
            );
            return null;
          }

          const {
            name,
            description,
            price,
            tags = [],
            images = [],
            manufacturer,
          } = productData;

          // 필수 필드 검사
          if (!name || !description || typeof price !== 'number') {
            console.warn(
              `[Product API] 상품 "${name}"의 필수 필드가 누락되었습니다.`
            );
            return null;
          }

         // 해시태그에 "전자제품"이 포함되어 있는지 정확히 확인
          const isElectronic =
            Array.isArray(tags) &&
            tags.some(
              (tag) => typeof tag === 'string' && tag.trim() === '전자제품'
            );

          if (isElectronic) {
            console.log(
              `[Product API] "${name}"을 ElectronicProduct로 생성합니다.`
            );
            return new ElectronicProduct(
              name,
              description,
              price,
              tags,
              images,
              manufacturer || 'Unknown'
            );
          } else {
            console.log(`[Product API] "${name}"을 Product로 생성합니다.`);
            return new Product(name, description, price, tags, images);
          }
        } catch (error) {
          console.error(
            `[Product API] 상품 ${index}번째 인스턴스 생성 실패:`,
            error.message
          );
          return null;
        }
      })
      .filter((product) => product !== null); // null 값 제거

    console.log(
      `[Product API] 총 ${products.length}개의 상품 인스턴스가 생성되었습니다.`
    );

   # // 콘솔 예쁘게 (배열 내부는 [Array]로 축약)
    const pretty = util.inspect(
      products.map((pr) => ({
        name: pr.name,
        price: pr.price,
        tags: pr.tags,
        images: pr.images,
        summary: pr.summary(),
      })),
      { depth: 1, colors: true, maxArrayLength: 0 }
    );

    console.log('products:', pretty);

   # // 단건 조회/생성/수정/삭제 샘플
    if (items[0]?.id) {
      console.log('[Product API] 첫 번째 상품 단건 조회...');
      const one = await getProduct(items[0].id);
      console.log('first product raw:', { id: one.id, name: one.name });
    }

   # // 테스트 상품 생성
    console.log('[Product API] 테스트 상품 생성...');
    const created = await createProduct({
      name: '테스트 상품',
      description: 'API로 생성된 테스트 상품입니다.',
      price: 9900,
      tags: ['테스트'],
      images: ['https://picsum.photos/640/480'],
    });
    console.log('created product:', created?.id);

    # // Bespoke AI 제트 400W 제품 생성
    console.log('[Product API] Bespoke AI 제트 400W 제품 생성...');
    const bespokeAI = await createBespokeAIJet();
    console.log('created Bespoke AI Jet:', {
      id: bespokeAI?.id,
      name: bespokeAI?.name,
      price: bespokeAI?.price,
      tags: bespokeAI?.tags,
    });

    # // 상품 수정 및 삭제
    if (created?.id) {
      console.log('[Product API] 상품 수정...');
      const patched = await patchProduct(created.id, {
        price: 12345,
        tags: ['테스트', '전자제품'],
      });
      console.log('patched price:', patched?.price);

      console.log('[Product API] 상품 삭제...');
      await deleteProduct(created.id);
      console.log('deleted product:', created.id);
    }
  } catch (e) {
    console.error('[Product API] Product flow error:', e.message);
    console.error('[Product API] Error details:', e);
  }

  # // ------- 클래스 동작 확인 -------
  const a = new Article('제목', '내용', '작성자');
  a.like();
  a.like();
  console.log('Article instance:', {
    title: a.title,
    likeCount: a.likeCount,
    createdAt: a.createdAt.toISOString(),
  });

  const p = new Product('일반상품', '설명', 1000, ['태그'], ['img']);
  const e = new ElectronicProduct(
    '전자상품',
    '설명',
    2000,
    ['전자제품'],
    ['img'],
    'ACME'
  );
  p.favorite();
  e.favorite();
  e.favorite();
  console.log(
    'Product favorite:',
    p.favoriteCount,
    '| Electronic favorite:',
    e.favoriteCount
  );
})();
