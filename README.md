# -
# // ArticleService.js

# // Article API 요청 함수들을 구현합니다.

# // https://panda-market-api-crud.vercel.app/docs 의 Article API를 사용합니다.

# import axios from 'axios';

# const BASE = 'https://panda-market-api-crud.vercel.app/articles';

# function okOrThrow(res, action) {

if (res.status >= 200 && res.status < 300) {
return res.data;
}

# const msg = `[Article API Error] ${action} -> ${res.status} ${res.statusText}`;

console.error(msg);
throw new Error(msg);
}

# export function getArticleList(page = 1, pageSize = 10, keyword = '') {

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

// 데모용 생성 함수
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
