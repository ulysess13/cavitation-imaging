% usage:
%
% hfig = overlay4d(im1, im2)
% hfig = overlay4d(im1, im2, hfig, hax, do_normslice, orientation_flag, overlay_flag, f_bot, f_top, cmap_flag, bgnd_bright, gamma_background, gamma_foreground, fov_scale_x, fov_scale_y)
%
% overlay im1 on im2
%
% im1 is a 4d array: (X,Y,T,Z)
% im2 is a 3d array: (X,Y,Z)

% bring im1 and im2 into the same space
% create 2d array for upsampled images

function [hfig,hax] = overlay4d(im1, im2, hfig, hax, do_normslice, orientation_flag, overlay_flag, f_bot, f_top, cmap_flag, bgnd_bright, gamma_background, gamma_foreground, fov_scale_x, fov_scale_y)

if (nargin<2)
    im2 = zeros(size(im1,1),size(im1,2),size(im1,4));
end;

if (nargin<3)
    hfig = 0;
end

if (nargin<4)
    hax = 0;
end

if (nargin<5)
    do_normslice = 0;
end

if (nargin<6)
    orientation_flag = 'col';
end

if (nargin<7)
    overlay_flag = 'abs';
end

if (nargin<8)
    f_bot = 0;
end

if (nargin<9)
    f_top = 1;
end

if (nargin<10)
    cmap_flag = 'jet';
end

if (nargin<11)
    bgnd_bright = 0.4;
end

if (nargin<12)
    gamma_background = 0.25;
end

if (nargin<13)
    gamma_foreground = 1;
end

if (nargin<14)
    fov_scale_x = 0.5;
end

if (nargin<15)
    fov_scale_y = 0.5;
end

[im1u,im2u] = upsample_image(im1,im2,fov_scale_x,fov_scale_y);

im1u_cat = cat_images(im1u, do_normslice, orientation_flag);
im2u_cat = cat_images(im2u, 1, orientation_flag);

im = get_channel(im1u_cat, overlay_flag);

im_max = max(im(:));
im2_max = max(abs(im2u_cat(:)));

im_wl = [f_bot*im_max f_top*im_max];
im2_wl = [0 im2_max];

if(hfig ~= 0)
    figure(hfig);
    newfig = 0;
else
    figure;
    hfig = gcf;
    newfig = 1;
end

if(hax ~= 0)
    axes(hax);
    newax = 0;
else
    hax = gca;
    newax = 1;
end

overlay_colormap = disp4d_overlay(im,im_wl,abs(im2u_cat),im2_wl,cmap_flag,bgnd_bright,gamma_background,gamma_foreground);

% fix figure
if(newax)
    set(hax,'Position',[0 0 1 1]);
end

set(hfig,'Position',[300 600 size(im1u_cat,2) size(im1u_cat,1)]);
