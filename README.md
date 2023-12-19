import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest, response: NextResponse) {
  const isAuthenticated = request.cookies.get("isAuthenticated")?.value == "true";
  const protectedPaths = ["/dashboard", "/archive", "/admin", "/account"];

  if (
    !isAuthenticated &&
    (protectedPaths.some(path => request.nextUrl.pathname.startsWith(path)) ||
      request.nextUrl.pathname.startsWith("/auth/create-account"))
  ) {
    return NextResponse.redirect(new URL("/auth/create-account", request.url));
  }

  if (
    isAuthenticated &&
    (request.nextUrl.pathname.startsWith("/auth/reset-password") ||
      request.nextUrl.pathname.startsWith("/auth/signin"))
  ) {
    return NextResponse.redirect(new URL("/", request.url));
  }

  return NextResponse.next();
}
