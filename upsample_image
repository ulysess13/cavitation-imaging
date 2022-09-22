% upsample_image()
%
% This script is used to prepare images for overlay.
function [im_u,dcm_u] = upsample_image(im,dcm,fov_scale_x,fov_scale_y,interp_flag,xo,yo)
% size of im = [xres yres Nt num_slices]
% size of dcm = [xdcm ydcm num_slices]

% TODO include check here

% extend dcm to [xdcm ydcm Nt num_slices]
% and overlay

% default interpolation method = nearest

% default fov_scale factors are 0.5 (crops half FOV)
% supports rectangular fov scaling

if(nargin<3)
    fov_scale_x = 0.5;
end
if(nargin<4)
    fov_scale_y = 0.5;
end

if(nargin<5)
    interp_flag = 1;
end

if(nargin<6)
    xo = round(size(dcm,1)/2);
end
if(nargin<7)
    yo = round(size(dcm,2)/2);
end

switch interp_flag
    case {1,'nearest'}
        interp_method = 'nearest';
    case {2,'bicubic'}
        interp_method = 'bicubic';
end

np = size(im,3);
nslices = size(im,4);

dcm_x = size(dcm,1);
dcm_y = size(dcm,2);

% for zero filling
x_new = ceil(dcm_x*(fov_scale_x));
y_new = ceil(dcm_y*(fov_scale_y));

x_new = ceil(x_new/2)*2;
y_new = ceil(y_new/2)*2;

crop_x = [xo-x_new/2+1:xo+x_new/2];
crop_y = [yo-y_new/2+1:yo+y_new/2];

% upsample to dcm resolution, then crop to desired FOV
im_u = zeros(x_new,y_new,np,nslices);
dcm_u = zeros(x_new,y_new,np,nslices);
for j=1:nslices
    for k=1:np
        im_tmp = imresize( squeeze(im(:,:,k,j)), [dcm_x,dcm_y],interp_method );
        try
            im_u(:,:,k,j) = im_tmp(crop_x,crop_y);
        catch
            try
                im_u(:,:,k,j) = im_tmp(ceil(crop_x),ceil(crop_y));
            catch
                im_u(:,:,k,j) = im_tmp;
            end
        end
        dcm_u(:,:,k,j) = dcm(crop_x,crop_y,j);
    end
end

